<template>
  <div class="container mb-4">
    <select class="custom-select" v-model="eventSelected">
      <option selected :value="null">
        {{ eventSelected ? "Todos" : "Tipo de evento" }}
      </option>

      <option v-for="event in events" :key="event.id" :value="event.id">
        {{ event.name }}
      </option>
    </select>
  </div>

  <div v-if="favorites.length === 0" class="emptyList">
    <span style="font-size: 120%" v-if="eventSelected">
      No tiene plantillas marcadas como favoritas coincidentes
    </span>
    <span style="font-size: 120%" v-else>
      No tiene plantillas marcadas como favoritas
    </span>
    <span @click="redirectTemplates" class="cursor-pointer redirectText mt-2">
      Ir a plantillas
    </span>
  </div>
  <div v-else class="container-xl text-left">
    <div class="row">
      <div
        v-for="(template, index) in favorites"
        :key="index"
        class="col-xs-12 col-sm-12 col-md-6 col-lg-4 col-xl-4"
      >
        <CardTemplate
          :template="template"
          favorite
          @refresh="getWebTemplatesFavorites()"
        />
      </div>
    </div>
  </div>
</template>
<script>
import CardTemplate from "./CardTemplate.vue";
import { useFavoritesStore } from "@/store/favorites.js";
import { useEventsStore } from "@/store/events.js";
import { useRouter } from "vue-router";
import { useToast } from "vue-toast-notification";

export default {
  components: {
    CardTemplate,
  },
  setup() {
    const useFavorite = useFavoritesStore();
    const useEvents = useEventsStore();
    const router = useRouter();
    const toast = useToast();

    return {
      useFavorite,
      useEvents,
      toast,
      router,
    };
  },
  data: () => ({
    eventSelected: null,
  }),
  computed: {
    favorites() {
      return (
        this.useFavorite.favorites.map((favorite) => favorite.WebTemplate) || []
      );
    },

    events() {
      return this.useEvents.events || [];
    },
  },
  mounted() {
    this.getEvents();
    this.getWebTemplatesFavorites();
  },
  methods: {
    redirectTemplates() {
      this.router.push("/templates");
    },

    async getEvents() {
      try {
        await this.useEvents.getAllEvents({ update: true });
      } catch (error) {
        this.toast.open({
          message: "Error al obtener eventos",
          type: "error",
          position: "top-right",
          dismissible: true,
        });
      }
    },

    async getWebTemplatesFavorites() {
      try {
        const hasEventSelected = this.eventSelected ?? false;

        const filters = {
          ...(hasEventSelected
            ? {
                event: this.eventSelected,
              }
            : {}),
        };

        await this.useFavorite.getAllWebTemplatesFavorites({
          filters,
          update: true,
        });
      } catch (error) {
        this.toast.open({
          message: "Error al obtener favoritos",
          type: "error",
          position: "top-right",
          dismissible: true,
        });
      }
    },
  },
  watch: {
    eventSelected(newValue, prevValue) {
      this.getWebTemplatesFavorites();
    },
  },
};
</script>
<style lang="scss" scoped>
.custom-select {
  padding: 0.5rem 3rem 0.5rem 2rem;
  font-size: 1rem;
  background-color: rgb(150, 61, 130);
  color: white;
  border: 2px solid rgb(150, 61, 130);
  border-radius: 2rem;
  -webkit-appearance: none;
  -moz-appearance: none;
  appearance: none;
  position: relative; /* Necesario para posicionar la flecha */
  background-image: url("data:image/svg+xml;utf8,<svg fill='%23fff' xmlns='http://www.w3.org/2000/svg' width='36' height='36' viewBox='0 0 24 24'><path d='M7 10l5 5 5-5z'/></svg>");
  background-repeat: no-repeat;
  background-position: right;
  background-size: 30px;
  transition: border-color 0.2s; /* Agrega una transición para suavizar el cambio de color del borde */
}

.custom-select:focus,
.custom-select:hover {
  border-color: white; /* Establece el color de borde para el estado de enfoque */
}

.custom-select::after {
  content: "\25BC"; /* Código de flecha hacia abajo */
  position: absolute;
  top: 50%;
  right: 1rem; /* Ajusta la posición de la flecha */
  transform: translateY(-50%);
  font-size: 24px; /* Tamaño de la flecha personalizada */
  color: white; /* Color blanco */
  pointer-events: none; /* Evita interacciones con la flecha */
}

.custom-select option:hover,
.custom-select option:focus {
  background-color: rgb(
    150,
    61,
    130
  ); /* Cambia el color de fondo cuando se pasa el mouse sobre las opciones */
  color: white; /* Cambia el color del texto a blanco cuando se pasa el mouse sobre las opciones */
}

.emptyList {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  margin-top: 2rem;

  .redirectText {
    color: var(--primary);
    font-weight: 600;

    &:hover {
      font-weight: 700;
    }
  }
}
</style>
