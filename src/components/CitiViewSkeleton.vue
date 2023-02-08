<template>
    <div class="flex flex-col flex-1 items-center">
        <!-- weather overview -->
        <div class="flex flex-col w-full items-center py-12">
            <AnimatedPlaceholder class="max-w-xs w-full py-3 mb-2" />
            <AnimatedPlaceholder class="max-w-xs w-full mb-12" />
            <AnimatedPlaceholder class="max-w-xs w-full py-10 mb-8" />
            <AnimatedPlaceholder class="max-w-xs w-full mb-2" />
            <AnimatedPlaceholder class="max-w-xs w-full mb-2" />
            <AnimatedPlaceholder class="max-w-xs w-full py-6 mb-8" />
        </div>
        <hr class="border-white border-opacity-10 border w-full" />

        <!-- hourly weather -->
        <div class="max-w-screen-md w-full py-12">
            <div class="mx-8 text-white">
                <AnimatedPlaceholder class="max-w-md mb-4" />
                <AnimatedPlaceholder class="max-w-lg mb-4" />
                <AnimatedPlaceholder class="max-w-lg mb-4" />
                <AnimatedPlaceholder class="max-w-lg mb-4" />
            </div>
        </div>

        <!-- weekly weather -->
        <div class="max-w-screen-md w-full py-12">
            <div class="mx-8 text-white">
                <AnimatedPlaceholder class="max-w-lg mb-4" />
            </div>
        </div>
    </div>
</template>

<script setup>
import AnimatedPlaceholder from './AnimatedPlaceholder.vue';

</script>