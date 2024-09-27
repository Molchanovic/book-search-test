<script lang="ts" setup>
import { postsApi, commentsApi } from '@/api'

interface IPost {
  title: string
  description: string
  views: number
  seo_description: string
  seo_title: string
  seo_keywords: string
}

interface IComment {
  id: string
  text: string
  postId: string
}

const route = useRoute()
const seoInfo = reactive({
  seo_description: '349384923423',
  seo_title: 'Duda',
  seo_keywords: '12342342134',
})
let comments = ref(null)

const { data: data } = await useFetch(`${postsApi}/${route.params.id}`)
const post: IPost = data
seoInfo['seo_title'] = post.value['seo_title']
seoInfo['seo_description'] = post.value['seo_description']
seoInfo['seo_keywords'] = post.value['seo_keywords']

useHead({
  title: seoInfo['seo_title'],
  meta: [
    { name: 'description', content: seoInfo['seo_description'] },
    { name: 'keywords', content: seoInfo['seo_keywords'] },
  ],
})

onMounted(async () => {
  const data = await $fetch(commentsApi, {
    method: 'GET',
  })
  comments.value = data.filter(
    (comment: IComment) => +route.params.id === +comment.postId
  )
})
</script>

<template>
  <div class="post">
    <div
      v-if="post"
      class="post-wrapper"
    >
      <h1 style="margin-bottom: 20px">{{ post?.title }}</h1>
      <p style="margin-bottom: 15px">{{ post?.description }}</p>
      <div
        style="
          display: inline-flex;
          border: 2px solid #000;
          border-radius: 14px;
          padding: 12px 20px;
        "
      >
        Wiews - {{ post?.views }}
      </div>
    </div>
    <h2>Comments</h2>
    <ul class="comments-block">
      <template v-for="comment of comments">
        <li
          v-if="comment.text"
          :key="comment.id"
          class="comment"
        >
          {{ comment.text }}
        </li>
      </template>
    </ul>
  </div>
</template>
