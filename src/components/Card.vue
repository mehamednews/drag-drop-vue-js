<template>
    <div>
        <div class="card">
            <slot></slot>
        </div>
    </div>
</template>

<script>
export default {
    props: {
        content: {}
    }
};
</script>

<style>
.card {
    background-color: rgb(57, 93, 255);
    border-radius: 3px;
    text-align: center;
    font-weight: 500;
    letter-spacing: 2px;
    padding: 1.5rem 2rem;
    color: #fff;

    width: 100%;
    margin-bottom: 0.5rem;
}
</style>
