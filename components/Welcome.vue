<template>
	<section class="flex flex-col">
		<h1 class="text-center py-4 font-bold text-3xl underline underline-offset-8 uppercase z-10 select-none">
			Chat App
		</h1>
		<div class="absolute p-4 bg-gray-300/50 inset-0 flex items-center justify-center flex-1">
			<div class="bg-white shadow-lg flex items-center h-[160px] p-4 rounded-lg w-full max-w-[300px]">
				<form @submit.prevent="startChatting" class="flex flex-col gap-2 w-full select-none">
					<p class="text-sm text-center font-semibold text-sky-900">Please enter name to start chatting</p>
					<Input autofocus required v-model="name" placeholder="Enter your name..." />
					<button class="bg-indigo-600 text-white h-10 rounded">Join Chat</button>
				</form>
			</div>
		</div>
	</section>
</template>

<script setup lang="ts">
const name = ref('');
const router = useRouter();

function startChatting() {
	const text = name.value.trim().replaceAll(' ', '+');
	if (!text) return;
	router.push(`/chat?name=${text}`);
}
</script>
