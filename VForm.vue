<template>
    <form :method="method" :action="action" @submit.prevent="submit($event)">
        <slot></slot>
        <!--<h5 class="text-center" v-if="isSubmitting">Das Formular wird übermittelt.</h5>-->
    </form>
</template>

<script>
    const SUCCESS_EVENT = 'event';
    const SUCCESS_REDIRECT = 'redirect';

    export default {
        props: {
            method: { type: String, default: 'GET' },
            action: { type: String, default: '/' },
            onSuccess: { default: SUCCESS_EVENT },
        },
        data() {
            return {
                isSubmitting: false
            }
        },
        methods: {
            submit($event) {
                this.hideErrors();
                this.toggleIsSubmitting(true);
                this.getSubmitButton();
                switch(this.method.toLowerCase()) {
                    case 'get':
                        axios.get(this.action).then(
                            response => { this.handleSuccess(response.data) },
                            errorResponse => { this.showErrors(errorResponse.response.data.errors) }
                        );
                        break;
                    case 'post':
                        const formData = new FormData($event.target);
                        axios.post(this.action, formData).then(
                            response => { this.handleSuccess(response.data) },
                            errorResponse => {
                                if(errorResponse.response.data.errors) this.showErrors(errorResponse.response.data.errors)
                                this.toggleIsSubmitting(false)
                            }
                        );
                        break;
                }
            },
            toggleIsSubmitting(state) {
                var formElements = this.$el.elements;
                for (var i = 0, len = formElements.length; i < len; ++i) {
                    formElements[i].readOnly = state;
                }
                this.isSubmitting = state;
                this.getSubmitButton().disabled = state;
            },
            showErrors(errors) {
                this.$emit('error');
                this.toggleIsSubmitting(false);
                this.getInputs().forEach(input => {
                    if(errors[input.name]) input.showErrors(errors[input.name])
                })
            },
            hideErrors() {
                this.getInputs().forEach(input => {
                    input.hideErrors()
                })
            },
            getInputs() {
                return this.$children.filter((child) => {
                    return child.$el.children[0] && child.$el.children[0].name;
                })
            },
            getSubmitButton() {
                return this.$el.querySelector('[type="submit"]')
            },
            handleSuccess(response) {
                switch (this.onSuccess) {
                    case SUCCESS_REDIRECT:
                        window.location.assign(response);
                        break;
                }
                this.toggleIsSubmitting(false);
                this.$emit('success', response);
            }
        }
    }
</script>