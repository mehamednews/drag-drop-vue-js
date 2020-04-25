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
        rgba(57, 93, 255, 0.04) 0px,
        rgba(57, 93, 255, 0.04) 2px,
        transparent 2px,
        transparent 13px
    );

    border-radius: 3px;
    margin-bottom: 0.5rem;
    z-index: -100;
}
</style>
