<template>
    <button class="simon__settings-item btn"
        :class="{active : button.value === level}"
        :disabled="isDisabled"
        @click="changeLevel()"
    >
        {{ button.text }}
        <icon-confirm class="icon"/>
    </button>
</template>

<script>
import IconConfirm from '@/components/icons/IconConfirm.vue';
export default {
    name: 'SettingsButton',

    components: { IconConfirm, },

    props: {
        button: {
            type: Object,
            required: true,
        },

        level: {
            type: String,
            required: true,
        },

        isDisabled: {
            type: Boolean,
            required: false,
        }
    },

    methods: {
        changeLevel() {
            this.$emit('update:level', this.button.value);
        }
    }
}
</script>

<style lang="scss" scoped>
.simon {
    &__settings-item {
        min-width: 150px;
        opacity: 0.5;
        &:disabled {
            cursor: default;
            opacity: 0.2;
            &.active {
                opacity: 0.4;
            }
        }
        & .icon {
            width: 30px;
            height: 30px;
            opacity: 0;
        }
        &.active {
            opacity: 1;
            & .icon {
                opacity: 1;
            }
        }
    }
}
</style>
