<template>
    <div class="simon">
        <div class="simon__alerts">
            <p class="simon__alerts-game-over"
                v-if="gameInfo.gameOver"
            >
                Вы проиграли
            </p>
        </div>
        <div class="simon__action">
            <div class="simon__field-w">
                <div class="simon__field">
                    <simon-button v-for="button of buttons" :key="button.id"
                        :button="button"
                        :isActive="button.id === gameInfo.currentButtonId"
                        :isDisabled="(!gameInfo.gameIsPlaying || gameInfo.subsequenceIsIterated)"
                        @choiceBtn="choiceBtn(button.id)"
                    />

                    <button class="simon__field-main-btn"
                        :disabled="gameInfo.gameIsPlaying"
                        @click="startPlay()"
                    >
                        <span v-if="!gameInfo.gameIsPlaying">
                            Старт
                        </span>
                        <span v-else>
                            {{ gameInfo.subsequence.length }}
                        </span>
                    </button>
                </div>
            </div>
            <div class="simon__settings-w">
                <button class="simon__settings-reset-btn btn btn_green"
                    v-if="gameInfo.gameIsPlaying"
                    :disabled="gameInfo.subsequenceIsIterated"
                    @click="resetGame()"
                >
                    Начать заново
                </button>
                <simon-settings
                    :settingsButtons="settingsButtons"
                    :level.sync="gameInfo.level"
                    :isDisabled="gameInfo.subsequenceIsIterated"
                    @update:level="changeLevel()"
                />
            </div>
        </div>
    </div>
</template>

<script>
import SimonButton from '@/components/SimonButton.vue'
import SimonSettings from '@/components/SimonSettings.vue'

export default {

    name: 'TheSimon',

    components: {
        SimonButton, SimonSettings,
    },

    data() {
        return {
            timeoutId: null,

            gameInfo: {
                gameIsPlaying: false,
                level: 'hard',
                subsequence: [],
                subsequenceIsIterated: false,
                currentButtonId: null,
                quantityClicksInRound: 0,
            },

            levels: {
                easy: {
                    time: 1500,
                },
                middle: {
                    time: 1000,
                },
                hard: {
                    time: 400,
                },
            },

            buttons: [
                {
                    id: 0,
                    color: 'red',
                },
                {
                    id: 1,
                    color: 'blue',
                },
                {
                    id: 2,
                    color: 'green',
                },
                {
                    id: 3,
                    color: 'yellow',
                },
            ],

            settingsButtons: {
                easy: {
                    id: 0,
                    text: 'Легкий',
                    value: 'easy',
                },
                middle: {
                    id: 1,
                    text: 'Средний',
                    value: 'middle',
                },
                hard: {
                    id: 2,
                    text: 'Сложный',
                    value: 'hard',
                },
            }
        }
    },

    methods: {
        startPlay: function() {
            this.gameInfo.gameIsPlaying = true
            this.gameInfo.gameOver = false
            this.startRound()
        },

        startRound: async function() {

            this.gameInfo.subsequenceIsIterated = true
            this.gameInfo.quantityClicksInRound = 0
            this.gameInfo.subsequence.push(this.getRandomFromInterval(0, 3))

            const { time } = this.levels[this.gameInfo.level]

            for (const [index, el] of this.gameInfo.subsequence.entries()) {

                if(!this.gameInfo.gameIsPlaying) return

                this.gameInfo.currentButtonId = null

                await new Promise(resolve => {
                    this.timeoutId = setTimeout(() => {
                        this.gameInfo.currentButtonId = el
                        resolve();
                    }, 100)
                });

                await new Promise(resolve => setTimeout(resolve, time))

            }

            this.gameInfo.currentButtonId = null
            this.gameInfo.subsequenceIsIterated = false

        },

        changeLevel: function() {
            this.resetGame()
        },

        resetGame: function() {
            clearTimeout(this.timeoutId);
            this.gameInfo.gameIsPlaying = false
            this.gameInfo.subsequenceIsIterated = false
            this.gameInfo.subsequence = []
            this.gameInfo.currentButtonId = null
            this.gameInfo.quantityClicksInRound = 0
        },

        gameOver: function() {
            this.gameInfo.gameOver = true
            this.resetGame()
        },

        choiceBtn: function(id) {
            if(this.gameInfo.subsequence[this.gameInfo.quantityClicksInRound] !== id) {
                this.gameOver()
            } else {
                this.gameInfo.quantityClicksInRound++
                if(this.gameInfo.subsequence.length === this.gameInfo.quantityClicksInRound) {
                    this.startRound()
                }
            }
        },

        getRandomFromInterval: function(min, max) {
            let rand = min + Math.random() * (max + 1 - min);
            return Math.floor(rand);
        },
    },
}
</script>

<style lang="scss" scoped>
.simon {
    display: flex;
    align-items: center;
    justify-content: center;
    flex-direction: column;
    min-height: 100vh;
    padding: 15px;

    &__alerts {
        min-height: 60px;
        &-game-over {
            color: red;
            font-size: 35px;
            line-height: 120%;
            font-weight: bold;
        }
    }

    &__action {
        display: flex;
        gap: 50px;
        justify-content: center;
    }
    &__field-w {
        background: rgba(0,0,49,0.6);
        padding: 20px;
        width: fit-content;
        border-radius: 50%;
    }

    &__field {
        position: relative;
        width: 400px;
        height: 400px;
        display: flex;
        flex-wrap: wrap;
        border-radius: 50%;
        overflow: hidden;
        gap: 20px;
    }

    &__field-main-btn {
        position: absolute;
        left: 50%;
        top: 50%;
        transform: translate(-50%, -50%);
        border-radius: 50%;
        border: 3px solid #111;
        width: 38%;
        height: 38%;
        background: radial-gradient(ellipse at center, rgba(0, 0, 0, 0) 0%, rgba(0, 0, 0, 0) 60%, #777777 80%), #004;
        color: #fff;
        box-shadow: 2px 2px 10px 0 rgba(0, 0, 0, 0.5);
        font-weight: bold;
        &:disabled {
            cursor: default;
        }
    }

    &__settings-w {
        position: relative;
        display: flex;
        align-items: center;
    }

    &__settings-reset-btn {
        position: absolute;
        top: 0;
        left: 0;
    }
}
</style>
