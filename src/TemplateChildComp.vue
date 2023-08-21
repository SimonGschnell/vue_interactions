
<script setup>

</script>
<template>
    <hr/>
    <p>template fragment provided by the parent:</p>
    <slot>
        <!-- we can specify the default fallback content if the parent does not provide any template fragement-->
        <p>Fallback content</p>
    </slot>
    <hr/>
</template>