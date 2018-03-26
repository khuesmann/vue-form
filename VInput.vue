<template>
    <div>
        <slot></slot>
        <div v-if="errorMessages"></div>
        <div v-if="errorMessages.length > 0" :class="errorClass">
            {{errorMessageString}}
        </div>
    </div>
</template>

<script>
    export default {
        name: 'v-input',
        data() {
            return {
                name: null,
                errorMessages: [],
                errorClass: 'help-block'
            }
        },
        mounted() {
            this.name = this.$el.children[0].name
        },
        computed: {
            errorMessageString() {
                return this.errorMessages.join(', ')
            }
        },
        methods: {
            showErrors(errorMessages) {
                this.errorMessages = errorMessages
            },
            hideErrors() {
                this.errorMessages = []
            },
            setErrorClass(errorClass) {
                this.errorClass = errorClass
            }
        }
    }
</script>