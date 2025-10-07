<template>
	<DefaultLayout :title="`Tag: ${route.params.tag}`">
		<div v-for="post in Posts" :key="post.path" class="post-item">
			<p class="date">{{ post.date?.split(' ')[0] }}</p>

			<p>
				<NuxtLink :to="getUrlByPost(post)">{{ post.title }}</NuxtLink>
			</p>
		</div>
	</DefaultLayout>
</template>

<script lang="ts" setup>
	import type { Post } from '~/types/post'

	const route = useRoute()

	const Posts = (
		await useAsyncData(`tag-${route.params.tag}`, () =>
			queryCollection('posts')
				.where('tags', '=', decodeURIComponent(route.params.tag as string))
				.order('date', 'DESC')
				.select('title', 'date', 'path')
				.all()
		)
	).data as Ref<Post[]>

	if (Posts.value.length === 0) {
		throw createError({ statusCode: 404, statusMessage: 'Tag not found', fatal: true })
	}
</script>

<style lang="less" scoped>
	.post-item {
		margin-bottom: 20px;
	}

	.date {
		color: #666;
		font-size: 0.9em;
	}
</style>
