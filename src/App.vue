<template>
  <header :class="{ inLogin: inLogin || inRegister }">
    <img
      alt="BeluEssence Creatives Logo"
      class="logo cursor-pointer"
      :class="{ inLogin: inLogin || inRegister }"
      src="/Essence_Creatives_Logo.png"
      width="244"
      height="152"
      @click="redirectHome"
    />

    <nav v-if="!inLogin && !inRegister">
      <RouterLink to="/">Inicio</RouterLink>
      <RouterLink to="/templates">Plantillas</RouterLink>
      <!-- <RouterLink to="/contact">Contacto</RouterLink> -->
      <RouterLink to="/favorites" v-if="isLogged">Favoritos</RouterLink>
      <RouterLink to="/history" v-if="isLogged">Historial</RouterLink>
      <button
        @click="redirectLogin"
        type="button"
        class="btn btn-primary mx-2"
        v-if="!isLogged"
      >
        Iniciar sesión
      </button>
      <UserDropdown v-if="isLogged" class="mx-2" />
    </nav>
  </header>

  <RouterView class="main-content" />

  <Footer />
</template>

<script>
import { RouterLink, RouterView } from "vue-router";
import { useRouter } from "vue-router";
import { useLoginStore } from "@/store/login.js";
import UserDropdown from "./components/UserDropdown.vue";
import Footer from "./components/Footer.vue";

export default {
  components: {
    Footer,
    UserDropdown,
  },
  setup() {
    const router = useRouter();

    const useLogin = useLoginStore();

    return {
      router,
      useLogin,
    };
  },
  computed: {
    inLogin() {
      return this.useLogin.inLogin;
    },

    inRegister() {
      return this.useLogin.inRegister;
    },

    isLogged() {
      return this.useLogin.isLogged;
    },
  },
  methods: {
    redirectHome() {
      this.router.push("/");
    },

    redirectLogin() {
      this.router.push("/login");
    },
  },
};
</script>

<style lang="scss" scoped>
header {
  line-height: 1.5;
  max-height: 100vh;
}

.main-content {
  min-height: calc(100vh - (100px));
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

nav {
  // display: flex;
  // justify-content: flex-end;
  width: 100%;
  font-size: 1rem;
  text-align: center;
  margin-top: 2rem;
  margin-bottom: 1em;
}

nav a.router-link-exact-active {
  color: var(--color-text);
}

nav a.router-link-exact-active:hover {
  background-color: transparent;
}

nav a {
  display: inline-block;
  padding: 0 1rem;
  /* border-left: 1px solid var(--color-border); */
}

@media (hover: hover) {
  a:hover {
    border-bottom: 1px solid var(--primary);
  }
}

@media (min-width: 1024px) {
  header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding-right: calc(var(--section-gap) / 4);

    &.inLogin {
      justify-content: center;
      padding-right: 0;
    }
  }

  .logo {
    margin: 0 2rem 0 0;

    &.inLogin {
      margin: 0 0 2rem 0;
    }
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }

  nav {
    text-align: right;
    margin-right: -1rem;
    font-size: 1rem;
    display: flex;
    justify-content: flex-end;
    align-items: center;
    padding: 1rem 0;
    margin-top: 1rem;
  }
}
</style>
