<template>
    <button class="button">
        <slot></slot>
    </button>
</template>

<style lang="scss" scoped>
@import "../../styles/app.scss";

.button {
    @include flex-center();
    width: 114px;
    height: 40px;
    box-sizing: border-box;
    background: $color-regular-turquoise;
    border: 1px solid $color-bright-turquoise;
    box-shadow: 0px 4px 4px $color-light-grey;
    border-radius: 14px;
    cursor: pointer;
    transition: background 0.2s ease-in-out;

    &:hover {
        background: $color-bright-turquoise;
    }
}
</style>