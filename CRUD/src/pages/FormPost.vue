<template>
    <q-page padding>
        <q-form
        @submit="onSubmit"
        class="row q-col-gutter-sm">
            <q-input
            filled
            v-model="form.title"
            label="Titulo"
            lazy-rules
            class="col-lg-6 col-xs-12"
            :rules="[ val => val && val.length > 0 || 'Campo obrigatorio']" />

            <q-input
            filled
            v-model="form.author"
            label="Author"
            lazy-rules
            class="col-lg-6 pol-xs-12"
            :rules="[ val => val && val.length > 0 || 'Campo obrigatorio']" />

            <div class="col-lg-12 col-xs-12">
            <q-editor
            v-model="form.content" />
            </div>

            <div class="col-12">
            <q-btn label="Salvar" color="primary" class="float-right" type="submit" />

            <q-btn label="Cancelar" color="white" class="float-right" text-color="primary" :to="{ name: 'home'}"/>
            </div>
        </q-form>
    </q-page>
</template>

<script>
import { defineComponent, ref, onMounted } from 'vue'
import postsServices from 'src/services/posts'
import { useQuasar } from 'quasar'
import { useRouter, useRoute } from 'vue-router'
export default defineComponent({
  name: 'FormPost',
  setup () {
    const { post, getById, update } = postsServices()
    const $q = useQuasar()
    const router = useRouter()
    const route = useRoute()
    const form = ref({
      title: '',
      content: '',
      author: ''
    })

    onMounted(async () => {
      if (route.params.id) {
        getPost(route.params.id)
      }
    })

    const getPost = async (id) => {
      try {
        const response = await getById(id)
        form.value = response
      } catch (error) {
        console.log(error)
      }
    }

    const onSubmit = async () => {
      try {
        if (form.value.id) {
          await update(form.value)
        } else {
          await post(form.value)
        }
        $q.notify({ message: 'Post salvo com sucesso!', icon: 'check', color: 'positive' })
        router.push({ name: 'home' })
      } catch (erro) {
        console.log(erro)
      }
    }

    return {
      form,
      onSubmit
    }
  }
})

</script>
