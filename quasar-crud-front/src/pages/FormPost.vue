<template>
  <q-page padding>
    <q-form
      @submit="onSubmit"
      class="row q-col-gutter-sm"
    >
      <q-input
        outlined
        v-model="form.title"
        label="Titulo"
        lazy-rules
        class="col-lg-6 col-xs-12"
        :rules="[(val) => (val && val.length >= 0) || 'Campo obrigatório']"
      />

      <q-input
        outlined
        v-model="form.author"
        label="Autor"
        lazy-rules
        class="col-lg-6 col-xs-12"
        :rules="[(val) => (val && val.length >= 0) || 'Campo obrigatório']"
      />

      <div class="col-lg-12 col-xs-12">
        <q-editor v-model="form.content" min-height="5rem" />
      </div>

      <div class="col-12 q-gutter-sm">
        <q-btn
          label="Salvar"
          color="primary"
          class="float-right"
          icon="save"
          type="submit"
        />
        <q-btn
          label="Cancelar"
          color="white"
          text-color="primary"
          class="float-right"
          :to="{ name: 'home' }"
        />
      </div>
    </q-form>
  </q-page>
</template>

<script>
import { defineComponent, ref, onMounted } from "vue";
import postService from "../services/posts";
import { useQuasar } from "quasar";
import { useRouter, useRoute } from "vue-router";

export default defineComponent({
  name: "FormPost",
  setup() {
    const { post, getById, update } = postService();
    const $q = useQuasar();
    const router = useRouter();
    const route = useRoute();
    const form = ref({
      title: "",
      content: "",
      author: "",
    });

    onMounted(async () => {
     if (route.params.id) {
       getPostById(route.params.id);
     }
    });

    const getPostById = async (id) => {
      try {
        const response = await getById(id);
        form.value = response;
      } catch (error) {
        console.error(error);
      }
    };

    const onSubmit = async () => {
      try {
        if (form.value.id) {
          await update(form.value);
        } else {
          await post(form.value);
        }

        $q.notify({
          color: "positive",
          message: "Post salvo com sucesso!",
          icon: "delete_forever",
          timeout: 2000,
        });
        router.push({ name: "home" });
      } catch (error) {
        throw new Error(error);
      }
    };

    return { form, onSubmit };
  },
});
</script>
