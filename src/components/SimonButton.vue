<template>
    <button class="simon__field-item"
        :class="[{active: isActive}, button.color]"
        :disabled="isDisabled"
        @click="handleClick()"
    ></button>
</template>

<script>
export default {
    
    name: 'SimonButton',

    props: {
        button: {
            type: Object,
            required: true,
        },

        isActive: {
            type: Boolean,
            required: true,
        },

        isDisabled: {
            type: Boolean,
            required: true,
        }
    },

    methods: {
        handleClick: function() {
            this.$emit('choiceBtn')
            this.playSound()
        },

        playSound: function() {
            const audio = new Audio();
            audio.src = require(`@/assets/sounds/${this.button.id}.wav`)
            console.log(audio.src)
            audio.play();
        }
    },

    watch: {
        isActive: function() {
            if(this.isActive) this.playSound()
        }
    }
}
</script>

<style lang="scss" scoped>
.simon {
    &__field-item {
        border: none;
        flex: 0 0 calc(50% - 10px);
        height: calc(50% - 10px);
        overflow: hidden;
        opacity: 0.3;
        &.active {
            opacity: 1;
        }
        &:disabled {
            cursor: default;
        }
        &:active {
            &:not(:disabled) {
                opacity: 1;
            }
        }
        &.blue {
            background: #1cb5e0;
        }
        &.red {
            background: #ef3b36;
        }
        &.yellow {
            background: #ffc371;
        }
        &.green {
            background: #34e89e;
        }
    }
}
</style>
