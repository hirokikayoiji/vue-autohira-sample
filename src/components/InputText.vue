<template>
    <div>
        <input type='text' v-model='value' v-realtime='autoValues'/>
        <p>{{value}}</p>
        <p>{{autoValues}}</p>
        <p>{{ hiragana() }}</p>
    </div>
</template>

<script>
export default {
    name: 'InputText',
    data: () => {
        return {
            value: '',
            autoValues: []
        }
    },
    computed: {
        internal_value: function () {
            this.$emit('input', this.value)
            console.log('internal_value', this.value)
            return this.value
        }
    },
    methods: {
        hiragana: function (){
            let result = ''
            this.autoValues.forEach(function (value){
                result += value
            })
            console.log('hiragana:', result)
            return result
        }
    },
    directives: {
        realtime: {
            bind: function (el, binding, vnode) {
                console.log('directives', 'realtime', 'bind')
                function initial (el, expression, vnode) {
                    el.count = 0
                    vnode.context[expression] = []
                }
                function Realtimekeyup (el, {expression}, vnode, event) {
                    console.log('Realtimekeyup', vnode.context[expression], el.value, el.count)
                    if (el.value) {
                        var array = el.value.match(/[ぁ-んー　]*$/g)
                        if (array){
                            console.log('array:', array[0])
                            if (array[0]){
                                vnode.context[expression][el.count] = array[0]
                            }
                        }
                    } else {
                        initial(el, expression, vnode)
                    }
                }
                function RealtimeCompositionend (el, {value, expression}, vnode) {
                    console.log('compositionend', vnode.context[expression], el.value, el.count)
                    if (el.value) {
                        var array = el.value.match(/[ぁ-んー　]*$/g)
                        if (array){
                            console.log('array:', array[0])
                            if (!array[0]){
                                el.count++
                            }
                        }
                    } else {
                        initial(el, expression, vnode)
                    }
                }

                el.value = binding.value
                el.count = 0
                el.addEventListener('keyup', (event) => Realtimekeyup(el, binding, vnode, event))
                el.addEventListener('compositionend', () => RealtimeCompositionend(el, binding, vnode))
            },
            update: function (el, {value, expression}, vnode) {
                console.log('directives', 'realtime', 'update', vnode.context[expression], value)
            }
        }
    }
}
</script>
