<img width="1436" alt="Example" src="https://github.com/ganyicz/alpinejs-codepuncher/assets/3823354/5d73eb3d-45fb-4249-83de-7c2baca276eb">

# Headless component for 2FA & PIN codes 
Ever noticed the smooth interaction with Stripe's two factor authentication?

Entering a verification code is becoming a part of our users' daily routine.

Make it a delightful experience. Forget boring inputs, give your users what they deserve!

* Next input autofocus
* Input validation
* Keyboard and mobile accessiblity
* Completely unstyled
* Zero markdown (only x- bindings)
* Less than 40 lines of code

[Click here for example](https://ganyicz.github.io/alpinejs-codepuncher/example.html)

## Usage

1. Copy the code from section below
2. Register the component ([docs](https://alpinejs.dev/globals/alpine-data))
3. Render the component like so:

```html
<div x-data="codePuncher" x-effect="() => filled && alert('Code entered')">
    <!-- for(1..6) -->
        <input x-bind="digit" />
    <!-- endfor -->
</div>
```

To capture the final code, we're using `x-effect` ([docs](https://alpinejs.dev/directives/effect)) to check whether the code has been fully entered according to the value of `filled`.

## Component code

```javascript
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
