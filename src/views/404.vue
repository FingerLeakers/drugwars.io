<template>
    <UiCenter>
        <h2>This page isn't available</h2>
        <p>
            The link you followed may be broken,
            <router-link :to="{ name: 'news' }">click here</router-link>
            to go to the homepage.
        </p>
    </UiCenter>
</template>
