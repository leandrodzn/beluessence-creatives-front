<template class="body">
  <div v-if="contacts.length === 0" class="emptyList">
    <span style="font-size: 120%">
      No has contactado por ninguna plantilla
    </span>
    <span> Encuentra la mejor para tu ocasión </span>
    <span @click="redirectTemplates" class="cursor-pointer redirectText mt-2">
      Ir a plantillas
    </span>
  </div>
  <div v-else class="container text-center">
    <div v-for="(contact, index) in contacts" :key="index">
      <div class="card mb-3">
        <div class="row">
          <div
            class="col-md-5 cursor-pointer"
            @click="
              redirectTemplate(
                contact.WebTemplate.id,
                contact.template_available
              )
            "
          >
            <img
              :src="contact.WebTemplate?.ThumbnailImage?.link || '/Essence_Creatives_Logo.png'"
              class="img-fluid rounded-start px-3 py-3 template-image"
              :alt="contact.WebTemplate.name"
            />
          </div>
          <div class="col-md-7">
            <div class="card-body">
              <div class="d-flex align-items-center justify-content-between">
                <h3
                  class="card-title cursor-pointer"
                  @click="
                    redirectTemplate(
                      contact.WebTemplate.id,
                      contact.template_available
                    )
                  "
                >
                  {{ contact.WebTemplate?.name }}
                </h3>
                <DropdownOptions :options="options(contact)" />
              </div>
              
              <div class="d-flex align-items-center gap-1">
                <vue-feather
                type="minus"
                size="30px"
                stroke="rgb(150, 61, 130)"
                fill="rgb(150, 61, 130)"
                ></vue-feather>
                <span class="badge text-bg-primary" v-if="contact.readed">Leído</span>
                <span class="badge text-bg-dark" v-if="!contact.show_admin">Eliminada por administrador</span>
              </div>
              
              <p
                class="card-text cursor-pointer"
                @click="
                  redirectTemplate(
                    contact.WebTemplate.id,
                    contact.template_available
                  )
                "
              >
                ${{ contact.price_day }} MXN
              </p>
              <p class="card-text">Te pusiste en contacto el día:</p>
              <p class="card-text">
                <small class="card-title">{{ contact.formattedDate }}</small>
              </p>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Modal Confirmation Delete -->
    <ConfirmationModal
      v-if="showModalDelete"
      :title="'Eliminar solicitud'"
      :message="'¿Está seguro de eliminar esta solicitud? Será removida de la lista y se actualizará su estado para los administradores'"
      @cancel="resetHistoryDelete"
      @confirm="deleteHistoryItem"
    />
  </div>
</template>
<script>
import { useHistoryStore } from "../store/history.js";
import { useRouter } from "vue-router";
import { useToast } from "vue-toast-notification";
import DropdownOptions from "./DropdownOptions.vue";
import ConfirmationModal from "./ConfirmationModal.vue";

export default {
  components: {
    DropdownOptions,
    ConfirmationModal
  },
  setup() {
    const useHistory = useHistoryStore();
    const router = useRouter();
    const toast = useToast();

    return {
      useHistory,
      router,
      toast,
    };
  },
  computed: {
    contacts() {
      return this.useHistory.history.map(history => ({...history, busy: false}));
    },
  },
  data() {
    return {
      showModalDelete: false,
      itemToDelete: null
    };
  },
  async mounted() {
    await this.getContactHistory();
  },
  methods: {
    redirectTemplate(id, template_available) {
      if (!template_available) {
        this.toast.open({
          message: "Plantilla no disponible por el momento",
          type: "error",
          position: "top-right",
          dismissible: true,
        });
        return;
      }
      this.router.push(`/template/${id}`);
    },

    redirectTemplates() {
      this.router.push("/templates");
    },

    async getContactHistory() {
      try {
        await this.useHistory.getAllTemplateContacts({
          update: true,
        });
      } catch (error) {
        this.toast.open({
          message: "Error al obtener historial de contacto",
          type: "error",
          position: "top-right",
          dismissible: true,
        });
      }
    },

    showModalConfirmationDelete (history) {
      if (!history?.id) return

      this.itemToDelete = history
      this.showModalDelete = true
    },

    resetHistoryDelete() {
      this.itemToDelete = null
      this.showModalDelete = false
    },

    async deleteHistoryItem() {
      if (!this.itemToDelete?.id) return

      try {
        this.itemToDelete.busy = true
        await this.useHistory.deleteWebTemplateContact({id: this.itemToDelete.id})

        this.toast.open({
          message: 'Solicitud eliminada',
          type: 'success',
          position: 'top-right',
          dismissible: true
        })

        this.getContactHistory()
      } catch (error) {
        this.toast.open({
          message: 'Error al eliminar solicitud',
          type: 'error',
          position: 'top-right',
          dismissible: true
        })
      } finally {
        this.itemToDelete.busy = false
        this.resetHistoryDelete()
      }
    },

    options(contact) {
      return [
        {
          label: 'Eliminar para mí',
          icon: 'trash-2',
          action: () => this.showModalConfirmationDelete(contact)
        }
      ]
    } 
  },
};
</script>
<style lang="scss" scoped>
.template-image {
  width: 100%; /* Ajusta la imagen al ancho completo del contenedor */
  height: 250px; /* Establece una altura fija para la imagen */
  object-fit: cover; /* Mantiene la relación de aspecto y recorta la imagen si es necesario */
  /* border-radius: 0px; Agrega bordes redondeados si es necesario */
}

.card-body {
  text-align: left;
}

.card-title {
  color: var(--primary);
}

.card {
  margin-bottom: 1.5em;
  background-color: rgb(237, 230, 222);
}

.card-text {
  font-size: x-large;
  font-weight: normal;
}

.emptyList {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  margin-top: 4rem;

  .redirectText {
    color: var(--primary);
    font-weight: 600;

    &:hover {
      font-weight: 700;
    }
  }
}
</style>
