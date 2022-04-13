<template>
  <q-page padding>
    <q-table title="Artigos" :rows="posts" :columns="columns" row-keys="name">
      <template v-slot:top>
        <span class="text-h5">Artigos</span>
        <q-space />
        <q-btn
          color="primary"
          label="Adicionar Artigo!"
          :to="{ name: 'formPost' }"
        />
      </template>
      <template v-slot:body-cell-actions="props">
        <q-td :props="props" class="q-gutter-sm">
          <q-btn
            icon="edit"
            color="info"
            dense
            size="sm"
            @click="handleEditPost(props.row.id)"
          />

          <q-btn
            icon="delete"
            color="negative"
            dense
            size="sm"
            @click="handleDeletePost(props.row.id)"
          />
        </q-td>
      </template>
    </q-table>
  </q-page>
</template>

<script>
import { defineComponent, onMounted, ref } from "vue";
import postService from "../services/posts";
import { useRouter } from "vue-router";
import { useQuasar } from "quasar";

export default defineComponent({
  name: "IndexPage",
  setup() {
    const posts = ref([]);
    const $q = useQuasar();
    const router = useRouter();
    const { list, remove } = postService();

    onMounted(() => {
      getPosts();
    });

    const getPosts = async () => {
      try {
        const data = await list();
        posts.value = data;
      } catch (error) {
        throw new Error(error);
      }
    };

    const handleEditPost = async (id) => {
      router.push({ name: "formPost", params: { id } });
    };

    const handleDeletePost = async (id) => {
      try {
        $q.dialog({
          title: "Remover",
          message: "Gostaria de apagar essa tarefa?",
          cancel: true,
          persistent: true,
        }).onOk(async () => {
          await remove(id);
          $q.notify({
            color: "positive",
            message: "Post deleted successfully",
            icon: "delete_forever",
            timeout: 2000,
          });
          await getPosts();
        });
      } catch (error) {
        $q.notify({
          color: "negative",
          message: "Erro ao apagar post",
          icon: "times",
          timeout: 2000,
        });
      }
    };

    const columns = [
      {
        name: "id",
        field: "id",
        label: "ID",
        align: "left",
        sortable: true,
      },
      {
        name: "title",
        align: "left",
        field: "title",
        label: "Titulo",
        sortable: true,
      },
      {
        name: "author",
        field: "author",
        align: "left",
        label: "Autor",
        sortable: true,
      },
      // {
      //   name: "content",
      //   field: "content",
      //   align: "left",
      //   label: "Conteudo",
      //   sortable: true,
      // },
      {
        name: "actions",
        field: "actions",
        align: "right",
        label: "Ações",
      },
    ];

    return {
      posts,
      columns,
      handleDeletePost,
      handleEditPost,
    };
  },
});
</script>
