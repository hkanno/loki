<template>
    <v-text-field
            v-money="moneyConfig"
            :name="name"
            :label="label"
            :maxLength="maxLength"
            :disabled="disabled"
            :required="required"
            :value="value"
            @input="updateValue($event)"
            @keydown="validatorNegative($event)"
            @keyup.enter="$emit('keyupEnter')"/>
</template>

<script>
    import accounting from 'accounting'

    export default {
        props: {
            value: {
                required: true
            },
            label: {
                type: String,
                default: ''
            },
            name: {
                type: String,
                default: ''
            },
            required: {
                type: Boolean,
                default: false
            },
            disabled: {
                type: Boolean,
                default: false
            },
            maxLength: {
                type: Number,
                default: 24
            },
            negative: {
                type: Boolean,
                default: false
            },
            precision: {
                type: Number,
                default: 2
            },
            prefix: {
                type: String,
                default: 'R$ '
            },
            suffix: {
                type: String,
                default: ''
            }
        },
        data() {
            return {
                moneyConfig: {}
            }
        },
        mounted() {
            this.moneyConfig = {
                decimal: ',',
                thousands: '.',
                prefix: this.prefix,
                suffix: this.suffix,
                precision: this.precision,
                masked: false
            }
        },
        methods: {
            updateValue(value) {
                let valueNumber = value
                if (this.prefix) {
                    valueNumber = valueNumber.substring(this.prefix.length - 1, valueNumber.length)
                }
                if (this.suffix) {
                    valueNumber = valueNumber.substring(0, valueNumber.length - this.suffix.length)
                }
                const valueFormated = accounting.unformat(valueNumber, ',')
                if (valueFormated !== this.value && this.validadePrecison(valueFormated)) {
                    this.$emit('input', valueFormated)
                }
            },
            validatorNegative($event) {
                if ($event.key === '-' && !this.negative) {
                    $event.preventDefault()
                }
            },
            validadePrecison(value) {
                const valueText = value + ''
                const valueParts = valueText.split('.')
                if (valueParts > 0 && valueParts[1].length > this.precision) {
                    return false
                }
                return true
            }
        }
    }
</script>

<style scoped lang="stylus">

</style>
