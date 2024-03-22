<template>
    <div class="simon__settings">
        <settings-button v-for="button of settingsButtons" :key="button.id"
            :button="button"
            :level="level"
            :isDisabled="isDisabled"
            v-on:update:level="changeLevel($event)"
        />
    </div>
</template>

<script>
import SettingsButton from '@/components/SettingsButton.vue'

export default {

    name: 'SimonSettings',

    components: { SettingsButton },

    props: {
        settingsButtons: {
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
        changeLevel(updatedLevel) {
            this.$emit('update:level', updatedLevel);
        }
    }
}
</script>

<style lang="scss" scoped>
.simon {

    &__settings {
        display: flex;
        flex-direction: column;
        gap: 10px;
        height: fit-content;
    }

    &__settings-item {
        &.active {
            & button {
                opacity: 1;
            }
        }
        & button {
            display: flex;
            align-items: center;
            text-align: center;
            justify-content: center;
            padding: 15px;
            border-radius: 12px;
            background: #006280;
            color: #fff;
            font-weight: bold;
            min-width: 150px;
            opacity: 0.5;
        }
    }
}

</style>
