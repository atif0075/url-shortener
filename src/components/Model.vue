<template>
  <main>
    <div xyz="delay-2 fade small rotate-right" class="m-10">
      <div v-if="showModal">
        <main class="model">
          <div class="inner-model relative">
            <h1 class="font-bold block text-2xl mb-3 text-center">
              {{ btnText }}
            </h1>
            <button
              v-if="show"
              xyz="inherit left"
              @click="cancel"
              class="btn text-up bg-red-400 top-0 font-bold absolute right-0"
            >
              X
            </button>
            <XyzTransitionGroup
              appear-visible
              mode="out-in"
              class="relative overflow-hidden"
            >
              <input
                v-if="isUserSignup"
                xyz="fade left-100%"
                :class="{ 'text-red-400 border-red-400': red }"
                class="input"
                v-model="name"
                type="text"
                placeholder="Name"
              />
              <input
                v-if="show"
                xyz="fade left-100% duration-7"
                :class="{ 'text-red-400 border-red-400': red }"
                class="mt-3 input"
                v-model="email"
                type="email"
                placeholder="Email"
              />

              <input
                v-if="show"
                xyz="fade left-100% duration-10"
                :class="{ 'text-red-400 border-red-400': red }"
                class="mt-3 input"
                v-model="password"
                type="password"
                placeholder="Password"
              />
            </XyzTransitionGroup>
            <div v-if="loading" class="flex justify-center">
              <img class="w-10" src="../assets/Infinity.svg" alt="" />
            </div>
            <XyzTransitionGroup
              appear-visible
              xyz="fade stagger"
              class="w-full text-right justify-end space-x-5 flex mt-5"
            >
              <button
                v-if="show"
                xyz="inherit up"
                class="btn bg-up font-bold"
                @click="store.GoogleSignup"
              >
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  class="w-6 h-6 text-red-500"
                  x="0px"
                  y="0px"
                  viewBox="0 0 30 30"
                  fill="currentColor"
                >
                  <path
                    d="M 15.003906 3 C 8.3749062 3 3 8.373 3 15 C 3 21.627 8.3749062 27 15.003906 27 C 25.013906 27 27.269078 17.707 26.330078 13 L 25 13 L 22.732422 13 L 15 13 L 15 17 L 22.738281 17 C 21.848702 20.448251 18.725955 23 15 23 C 10.582 23 7 19.418 7 15 C 7 10.582 10.582 7 15 7 C 17.009 7 18.839141 7.74575 20.244141 8.96875 L 23.085938 6.1289062 C 20.951937 4.1849063 18.116906 3 15.003906 3 z"
                  ></path>
                </svg>
              </button>
              <button
                v-if="show"
                xyz="fade left-100% duration-10"
                class="btn bg-up font-bold"
                @click="store.GitSignup"
              >
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  x="0px"
                  y="0px"
                  class="w-6 h-6 text-back"
                  fill="currentColor"
                  viewBox="0 0 30 30"
                >
                  <path
                    d="M15,3C8.373,3,3,8.373,3,15c0,5.623,3.872,10.328,9.092,11.63C12.036,26.468,12,26.28,12,26.047v-2.051 c-0.487,0-1.303,0-1.508,0c-0.821,0-1.551-0.353-1.905-1.009c-0.393-0.729-0.461-1.844-1.435-2.526 c-0.289-0.227-0.069-0.486,0.264-0.451c0.615,0.174,1.125,0.596,1.605,1.222c0.478,0.627,0.703,0.769,1.596,0.769 c0.433,0,1.081-0.025,1.691-0.121c0.328-0.833,0.895-1.6,1.588-1.962c-3.996-0.411-5.903-2.399-5.903-5.098 c0-1.162,0.495-2.286,1.336-3.233C9.053,10.647,8.706,8.73,9.435,8c1.798,0,2.885,1.166,3.146,1.481C13.477,9.174,14.461,9,15.495,9 c1.036,0,2.024,0.174,2.922,0.483C18.675,9.17,19.763,8,21.565,8c0.732,0.731,0.381,2.656,0.102,3.594 c0.836,0.945,1.328,2.066,1.328,3.226c0,2.697-1.904,4.684-5.894,5.097C18.199,20.49,19,22.1,19,23.313v2.734 c0,0.104-0.023,0.179-0.035,0.268C23.641,24.676,27,20.236,27,15C27,8.373,21.627,3,15,3z"
                  ></path>
                </svg>
              </button>
              <button
                v-if="show"
                xyz="inherit right"
                class="btn bg-amber-500 font-bold"
                @click="signin"
              >
                {{ btnText }}
              </button>
            </XyzTransitionGroup>
            <div class="py-2">
              <p class="text-xs sm:text-sm text-right">
                Not a member yet?
                <span
                  @click="isUserSignupCheck"
                  class="text-amber-500 cursor-pointer underline"
                  >{{ showText }} here</span
                >
              </p>
            </div>
          </div>
        </main>
      </div>
    </div>
    <main class="h-96 px-12 flex justify-center items-center">
      <button @click="open" class="btn-out">Signin to keep record</button>
    </main>
    <main class="absolute bottom-2 z-50 sm:right-10 right-2">
      <Message :value="red" text="Please Validate All Inputs" />
    </main>
  </main>
</template>
<script lang="ts" setup>
import { doc, getDoc, setDoc } from "@firebase/firestore";
import { onMounted, ref } from "vue";
import { db } from "../Firebase/config";
import Message from "./Message.vue";
import { useStore } from "../store/Store";
let loading = ref(false);
let showModal = ref(false);
let name = ref();
let email = ref();
let password = ref();
let red = ref(false);
let isUserSignup = ref(false);
let showText = ref("Signup");
let btnText = ref("Signin");
let show = ref(true);
let store = useStore();
const reg =
  /^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
let isUserSignupCheck = () => {
  isUserSignup.value = !isUserSignup.value;
  if (isUserSignup.value) {
    showText.value = "Signin";
    btnText.value = "Signup";
  } else {
    showText.value = "Signup";
    btnText.value = "Signin";
  }
};
const signin = () => {
  if (email.value == null || password.value == null) {
    red.value = true;
    setTimeout(() => {
      red.value = false;
    }, 2000);
  } else if (!reg.test(email.value)) {
    red.value = true;
    setTimeout(() => {
      red.value = false;
    }, 2000);
  } else {
    red.value = false;
    if (btnText.value === "Signin") {
      SingInUser();
    } else {
      SingUpUser();
    }
  }
};

const SingInUser = async () => {
  try {
    loading.value = true;
    await store.signin({
      email: email.value,
      password: password.value,
    });

    const docRef = doc(db, "Users", store.user?.uid.toString());
    const docSnap = await getDoc(docRef);
    if (docSnap.exists()) {
      console.log("Document data:", docSnap.data());
    } else {
      console.log("No such document!");
    }
    loading.value = false;
  } catch (err) {
    console.log(err);
  }
};
const SingUpUser = async () => {
  try {
    loading.value = true;
    await store.signup({
      name: name.value,
      email: email.value,
      password: password.value,
    });
    const data = {
      Name: name.value,
      Email: email.value,
    };
    await setDoc(doc(db, "Users", `${store.user?.uid}`), data);
    loading.value = false;
  } catch (error) {
    console.log(error);
  }
};
const cancel = () => {
  show.value = false;
  setTimeout(() => {
    showModal.value = false;
  }, 700);
};
const open = () => {
  showModal.value = true;
  show.value = true;
};
</script>
<style scoped></style>
