<template>
    <div>
        <Suspense>
            <AsyncCityView />
            <template #fallback>
                <CitiViewSkeleton />
            </template>
        </Suspense>
    </div>
</template>

<script setup>
import AsyncCityView from '../components/AsyncCityView.vue';
import CitiViewSkeleton from '../components/CitiViewSkeleton.vue';


</script>
