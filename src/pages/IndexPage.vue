<template>
  <q-page padding>
    <q-table
      :dense="$q.screen.lt.md"
      title="Postagens"
      :rows="posts"
      :columns="columns"
      row-key="name"
      >
      <template v-slot:top>
        <span class="text-h5">Postagens</span>
        <q-space/>
        <q-btn color="primary"  label="Novo" :to="{ name: 'formPost'}" />
      </template>
      <template  v-slot:body-cell-actions="props">
        <q-td :props="props" class="q-gutter-sm" >
          <q-btn icon="edit" color="info" dense size="md" @click="handleEditPost(props.row.id)" />
          <q-btn icon="delete" color="negative" dense size="md" @click="handleDeletePost(props.row.id)" />
        </q-td>
      </template>
    </q-table>
  </q-page>
</template>

<script>
import { defineComponent, ref, onMounted } from 'vue'
import postsService from 'src/services/posts'
import { useQuasar } from 'quasar'
import { useRouter } from 'vue-router'

export default defineComponent({
  name: 'IndexPage',
  setup () {
    const posts = ref ([])
    const { list, remove } = postsService()
    const columns = [
      { name: 'id', field: 'id', label: 'id',  sortable: true, align: 'left' },
      { name: 'title', field: 'title', label: 'title',  sortable: true,  align: 'left'  },
      { name: 'author', field: 'author', label: 'Autor',  sortable: true,  align: 'left'  },
      { name: 'detalhes', field: 'detalhes', label: 'detalhes',  sortable: true,  align: 'left'  },
      { name: 'date', field: 'date', label: 'data',  sortable: true,  align: 'left'  },
      { name: 'actions', field: 'actions', label: 'actions',  align: 'rigth'  }
    ]

    const $q = useQuasar()
    const router = useRouter()

      
    
    onMounted(() => {
      getPosts()
    })
    const getPosts = async () => {
      try {
        const  data  = await list()
        posts.value = data
      } catch (error) {
        console.error(error)
      }
    }

    const handleDeletePost = async (id) => {
      try {
          $q.dialog({
          title: 'Deletar',
          message: 'Deseja deletar esse post ? ',
          cancel: true,
          persistent: true
        }).onOk(async () => {
          await remove(id)
          $q.notify({ message: 'arquivo apagado com sucesso', icon: 'check', color: 'positive' })
          await getPosts()
        })
      } catch (error) {
        $q.notify({ message: 'Erro ao pagar post', icon: 'times', color: 'negative' })
      }
    }

    const handleEditPost = (id) => {
      router.push({ name: 'formPost', params: { id } })
    }
    return {
      posts,
      columns,
      handleDeletePost,
      handleEditPost
    }
  }
})
</script>