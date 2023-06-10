<img width="1436" alt="Example" src="https://github.com/ganyicz/alpinejs-codepuncher/assets/3823354/5d73eb3d-45fb-4249-83de-7c2baca276eb">

# Headless component for 2FA & PIN codes 
Ever noticed the smooth interaction with Stripe's two factor authentication?

Entering a verification code is becoming a part of our users' daily routine.

Make it a delightful experience. Forget boring inputs, give your users what they deserve!

* Next input autofocus
* Input validation
* Keyboard and mobile accessiblity
* Paste handling
* Completely unstyled
* Zero markdown (only x- bindings)
* Copy & Paste into your project

[Click here for example](https://ganyicz.github.io/alpinejs-codepuncher/example.html)

## Requirements

* Alpine.js 3.x [https://alpinejs.dev/essentials/installation](https://alpinejs.dev/essentials/installation)
* Alpine.js Mask Plugin [https://alpinejs.dev/plugins/mask](https://alpinejs.dev/plugins/mask)

## Usage

Simply copy the following code to your project.

```html
<div x-data="{ code: null }">
    <div x-bind="codePuncher" x-model="code">
        <input x-bind="digit" />
        <input x-bind="digit" />
        <input x-bind="digit" />
        <input x-bind="digit" />
    </div>
</div>

<script>
    document.addEventListener('alpine:init', () => {
        Alpine.bind('codePuncher', () => ({
            'x-modelable': 'glued',
            'x-data': () => ({
                digits: [],
                inputs: [],
                glued: '',
                init() {
                    this.inputs = Array.from(this.$root.querySelectorAll('[x-bind="digit"]'));
                    this.digits = Array(this.inputs.length).fill(null)

                    this.$watch('digits', (value) => this.glued = value.join(""))
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
                handlePaste(index, event) {
                    const pastedDigits = String(event.clipboardData.getData('text/plain')).match(/\d/g) ?? []
                    const queuedDigits = pastedDigits.slice(0, this.digits.length - index)
                    const focusedInputIndex = Math.min(index + queuedDigits.length, this.digits.length - 1)

                    queuedDigits.forEach((value, i) => this.digits[index + i] = value)

                    this.$nextTick(() => this.inputs[focusedInputIndex].focus())
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
                        'x-bind:disabled': 'index > glued.length',
                        'x-bind:tabindex': 'index < glued.length ? -1 : 0',
                        'x-on:focus': '!last && clearCodeFrom(index)',
                        'x-on:input': '!last && focusNextInput(index)',
                        'x-on:keydown.left': '!first && focusPreviousInput(index)',
                        'x-on:keydown.backspace': '!first && (last && $el.value ? $el.value = null : focusPreviousInput(index))',
                        'x-on:paste.prevent' : 'handlePaste(index, event)',
                        'inputmode': 'numeric',
                    }
                }
            })
        }))
    })
</script>
```

## Styling

Since the component does not come with any styles, you will need to add yours. Feel free to alter the markdown in any way you need, just make sure the digits are inside the component. If you are looking for a stylized example [click here](https://ganyicz.github.io/alpinejs-codepuncher/example.html).
