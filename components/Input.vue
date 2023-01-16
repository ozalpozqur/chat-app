<template>
	<input
		@input="$emit('update:modelValue', $event.target.value)"
		:value="modelValue"
		class="block w-full shadow-sm focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm border-gray-300 rounded-md !text-base"
		type="text"
	/>
</template>

<script setup lang="ts">
defineProps<{
	modelValue?: string | boolean | number;
}>();
</script>
