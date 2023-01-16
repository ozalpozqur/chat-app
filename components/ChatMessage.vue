<template>
	<div
		class="bg-gray-200 w-fit max-w-[90%] sm:max-w-[80%] md:max-w-[60%] p-2 rounded flex flex-col"
		:class="{ 'self-end': isMe }"
	>
		<p class="w-fit text-sm sm:text-base">{{ message.content }}</p>
		<time class="text-[8px] text-right justify-self-end text-slate-500">{{ formatDate(message.createdAt) }}</time>
	</div>
</template>

<script setup>
import { format } from 'date-fns';
defineProps({
	message: {
		type: Object,
		required: true,
		default: {}
	},
	isMe: {
		type: Boolean,
		default: false,
		required: true
	}
});

function formatDate(date) {
	if (date instanceof Date) return format(date, 'PPpp');
	return format(new Date(date), 'PPpp');
}
</script>
