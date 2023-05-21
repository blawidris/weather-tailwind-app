<template lang="">
    <div>
        <Suspense>
            <AsyncCityView/>

            <template #fallback>
                <div>
                    loading...
                </div>
            </template>
        </Suspense>
    </div>
</template>


<script setup>
import AsyncCityView from '@/components/AsyncCityView';

// export default {
    
// }
</script>
