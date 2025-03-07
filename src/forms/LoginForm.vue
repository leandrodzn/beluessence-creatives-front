<template>
  <div class="form text-center mt-2">
    <h2 class="title">Iniciar sesión</h2>
    <div class="mb-3">Ingrese sus credenciales</div>
    <form class="mb-4" @submit.prevent="login">
      <div
        class="form-floating mb-3"
        :class="{ error: v$.email.$errors.length }"
      >
        <input
          v-model="email"
          class="form-control"
          type="email"
          placeholder="Correo electrónico"
          id="floatingInputSubject"
        />
        <label for="floatingInputSubject">Correo electrónico</label>
        <div v-if="v$.email.$error" class="validation-text">
          Correo electrónico es requerido
        </div>
      </div>

      <div class="mb-3">
        <div class="input-group">
          <div
            class="form-floating"
            :class="{ error: v$.password.$errors.length }"
          >
            <input
              v-model="password"
              class="form-control"
              :type="showPassword ? 'text' : 'password'"
              placeholder="Contraseña"
              id="floatingInputSubject"
            />
            <label for="floatingInputSubject">Contraseña</label>
          </div>
          <button
            class="btn btn-outline-primary d-flex align-items-center justify-content-center"
            type="button"
            @click="showPassword = !showPassword"
          >
            <vue-feather
              :type="showPassword ? 'eye-off' : 'eye'"
              size="15"
            ></vue-feather>
          </button>
        </div>
        <div v-if="v$.password.$error" class="validation-text mb-2">
          Contraseña es requerida
        </div>
      </div>

      <!-- <RouterLink to="/history" class="forgot-password">
          ¿Olvidaste tu contraseña?
        </RouterLink> -->

      <button type="submit" class="btn btn-primary">Iniciar sesión</button>
    </form>

    <div class="mb-0">¿No tienes una cuenta?</div>
    <RouterLink to="/register" class="register-link">Regístrate</RouterLink>
  </div>
</template>
<script>
import { useVuelidate } from "@vuelidate/core";
import { email, required } from "@vuelidate/validators";
import { RouterLink, useRouter } from "vue-router";
import { useLoginStore } from "@/store/login.js";
import { useToast } from "vue-toast-notification";

export default {
  components: {
    RouterLink,
  },
  setup() {
    const useLogin = useLoginStore();
    const toast = useToast();
    const router = useRouter();
    const v$ = useVuelidate();

    return { v$, useLogin, toast, router };
  },
  data() {
    return {
      email: "",
      password: "",
      showPassword: false,
    };
  },
  validations() {
    return {
      email: { email, required },
      password: { required },
    };
  },

  methods: {
    async login() {
      try {
        const isFormCorrect = await this.v$.$validate();

        if (!isFormCorrect) return;

        const data = {
          email: this.email,
          password: this.password,
        };

        const response = await this.useLogin.login({ data });

        this.toast.open({
          message: `Ha iniciado sesión como ${response.user.name} ${response.user.surname}`,
          type: "info",
          position: "top-right",
          dismissible: true,
        });

        setTimeout(() => {
          this.router.push("/");
        }, 500);
      } catch (error) {
        if (error?.name === "Invalid") {
          this.toast.open({
            message: "Credenciales inválidas, inténtelo de nuevo",
            type: "warning",
            position: "top-right",
            dismissible: true,
          });
        } else {
          this.toast.open({
            message: "Error al iniciar sesión",
            type: "error",
            position: "top-right",
            dismissible: true,
          });
        }
      }
    },
  },
};
</script>
<style lang="scss" scoped>
.title {
  font-weight: bold;
  color: var(--primary);
}
.form {
  display: flex;
  flex-direction: column;
  // align-items: center;
  // justify-content: center;
  // width: 100%;

  .validation-text {
    // font-weight: 400;
    margin: 5px;
    font-size: 80%;
    color: var(--primary);
  }

  .forgot-password {
    font-weight: 400;
    font-size: 95%;
    color: var(--primary);

    &:hover {
      font-weight: 500;
    }
  }
}

.register-link {
  font-weight: 500;
  font-size: 120%;

  &:hover {
    font-weight: 700;
  }
}
</style>
