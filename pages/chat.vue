<template>
	<section class="chat grid grid-rows-[1fr_auto] gap-1">
		<div
			ref="messageContainer"
			class="relative max-h-full scroll-smooth flex gap-2 flex-col snap-y overflow-auto rounded border p-3"
		>
			<ChatMessage :key="index" :is-me="message.isMe" :message="message" v-for="(message, index) in messages" />
		</div>
		<form class="flex items-center gap-1 py-1" @submit.prevent="submitHandler">
			<Input autofocus v-model="message" placeholder="Type here..." class="flex-1" required />
			<button
				type="submit"
				class="aspect-square text-gray-600 h-full flex items-center justify-center border rounded-md border-gray-300 transition active:ring-2 active:ring-indigo-500 hover:border-indigo-500"
			>
				<Icon size="20" name="fluent:send-16-regular" />
			</button>
		</form>
	</section>
</template>

<script setup lang="ts">
import altogic from '~/libs/altogic';
import { useInfiniteScroll } from '@vueuse/core';

const { name } = useRoute().query;
const router = useRouter();
const message = ref('');
const socketId = ref('');
const messageContainer = ref<HTMLDivElement>();
const messages = ref<object[]>([]);
const paginationData = ref<object>({});
const page = ref(1);

onMounted(() => {
	if (!name) return router.push('/');
	goToLastMessage();
	init();
});
onUnmounted(() => {
	altogic.realtime.off('message', onMessageHandler);
});
watch(messages, () => {
	goToLastMessage();
});
watch(page, () => {
	getMessages();
});

useInfiniteScroll(
	messageContainer,
	() => {
		// @ts-ignore
		if (paginationData.value?.totalPages === page.value) return;
		page.value++;
	},
	{ distance: 10, direction: 'top' }
);

async function init() {
	altogic.realtime.join('chat');
	altogic.realtime.on('message', onMessageHandler);
	altogic.realtime.onConnect(() => {
		socketId.value = altogic.realtime.getSocketId();
		altogic.realtime.updateProfile({ name, socketId: socketId.value });
	});
	getMessages().catch(err => console.error(err));
}
async function getMessages() {
	const {
		// @ts-ignore
		data: { data, info },
		errors
	} = await altogic.db.model('messages').page(page.value).sort('createdAt', 'desc').limit(100).get(true);
	if (errors) return alert("Couldn't get messages");
	paginationData.value = info;
	messages.value = [...messages.value, ...data].sort(
		(a, b) => new Date(a.createdAt).getTime() - new Date(b.createdAt).getTime()
	);
}

function submitHandler() {
	const text = message.value.trim();
	if (!text) return;
	sendMessage();
	message.value = '';
}

async function sendMessage() {
	const _message = {
		senderName: name,
		content: message.value,
		socketId: socketId.value
	};
	messages.value = [
		...messages.value,
		...[
			{
				isMe: true,
				..._message,
				createdAt: new Date()
			}
		]
	];
	const { data, errors } = await altogic.db.model('messages').create(_message);
	if (!errors) {
		altogic.realtime.send('chat', 'message', data);
	} else {
		alert('Something went wrong');
	}
}

function onMessageHandler(payload: any) {
	const { socketId: senderSocketId } = payload.message;
	if (senderSocketId === socketId.value) return;
	messages.value = [...messages.value, payload.message];
}
function goToLastMessage() {
	setTimeout(() => {
		if (!messageContainer.value) return;
		messageContainer.value.lastElementChild?.scrollIntoView();
	}, 250);
}
</script>

<style scoped>
.chat {
	height: calc(100vh - (var(--mainPadding) * 2));
}
</style>
