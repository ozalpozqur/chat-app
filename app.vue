<template>
	<main id="main" class="fixed inset-0 overflow-hidden grid p-3">
		<NuxtPage />
	</main>
</template>

<style>
:root {
	--mainPadding: 0.75rem;
}
html,
body,
#__nuxt {
	height: 100%;
}

.main {
	padding-block: var(--mainPadding);
}
</style>
