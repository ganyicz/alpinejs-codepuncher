# Alpine.js Codepuncher
Headless component for PIN codes, SMS codes, two-factor auth and more

<img width="1436" alt="Screenshot 2023-06-03 at 12 45 59" src="https://github.com/ganyicz/alpinejs-codepuncher/assets/3823354/5d73eb3d-45fb-4249-83de-7c2baca276eb">

```javascript
// Copy the code ----->

Alpine.data('codePuncher', () => ({
    digits: [],
    inputs: [],
    init() {
        this.inputs = Array.from(this.$root.querySelectorAll('[x-bind="digit"]'));
        this.digits = Array(this.inputs.length).fill(null)

        this.$nextTick(() => this.inputs[0].focus())
    },
    get filled() {
        return this.digits.join("").length === this.digits.length
    },
    clearCodeFrom(index) {
        this.digits = this.digits.map((value, i) => i >= index ? null : value)
    },
    focusPreviousInput(index) {
        this.$nextTick(() => this.inputs[index - 1].focus())
    },
    focusNextInput(index) {
        this.$nextTick(() => this.$el.value && this.inputs[index + 1].focus())
    },
    digit (index) {
        return {
            'x-data': `{
                index: inputs.indexOf($el),
                get first() { return this.index === 0 },
                get last() { return this.index === this.digits.length - 1 },
            }`,
            'x-mask': '9',
            'x-model': 'digits[index]',
            'x-bind:disabled': 'index > digits.join("").length',
            'x-bind:tabindex': 'index < digits.join("").length ? -1 : 0',
            'x-on:focus': '!last && clearCodeFrom(index)',
            'x-on:input': '!last && focusNextInput(index)',
            'x-on:keydown.left': '!first && focusPreviousInput(index)',
            'x-on:keydown.backspace': '!first && (last && $el.value ? $el.value = null : focusPreviousInput(index))',
            'inputmode': 'numeric',
        }
    }
}))
```
