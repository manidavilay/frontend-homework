<template>
    <section class="card">
        <header class="card__header">
            <img :src="logo" alt="Secret Sauce Partners logo">
            <img :src="logoText" alt="Secret Sauce Partners title" class="card__logo-text">
        </header>
        <main class="card__main">
            <slot>
            </slot>
        </main>
    </section>
</template>

<script>
export default {
    data() {
        return {
            logo: require('../../assets/logo.svg'),
            logoText: require('../../assets/logo-text.svg')
        }
    }
}
</script>


<style lang="scss">
@import '../../styles/app.scss';

.card {
    @include flex-center();
    flex-direction: column;
    width: 307px;
    min-height: 229px;
    border-radius: 14px;
    border: 1px solid $color-light-grey;

    &__header {
        @include align-center();
        justify-content: flex-start;
        width: 100%;
        height: 40px;
        padding: 0 26px;
        box-sizing: border-box;
        border-top-left-radius: 14px;
        border-top-right-radius: 14px;
        background-color: $color-light-turquoise;
    }

    &__logo-text {
        margin: 0 0 0 5px;
    }

    &__main {
        width: 100%;
        min-height: 189px;
        border-bottom-left-radius: 14px;
        border-bottom-right-radius: 14px;
        background-color: $color-grey;
    }
}
</style>