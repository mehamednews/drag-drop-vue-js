<template>
    <div class="placeholder" :style="{width:`${w}px !important`,height:`${h}px !important`}"></div>
</template>

<script>
export default {
    props: ['content', 'w', 'h']
};
</script>

<style>
.placeholder {
    background-image: repeating-linear-gradient(
        -45deg,
        rgba(57, 93, 255, 0.03) 0px,
        rgba(57, 93, 255, 0.03) 6px,
        transparent 6px,
        transparent 12px
    );
    border-radius: 3px;
    margin-bottom: 0.5rem;
}
</style>
