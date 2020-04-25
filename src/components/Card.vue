<template>
    <div class="card" :class="{transparent:isTransparent}">
        <slot></slot>
    </div>
</template>

<script>
export default {
    props: {
        isTransparent: {
            default: false
        }
    }
};
</script>

<style>
.card {
    background-color: rgb(62, 75, 255);
    text-align: center;
    font-size: 20px;
    font-weight: 500;
    letter-spacing: 2px;
    padding: 1.5em 0;
    color: #fff;
    white-space: pre-line;

    width: 100%;
    margin-bottom: 0.5rem;
}

.transparent {
    opacity: 0.7;
}
</style>
