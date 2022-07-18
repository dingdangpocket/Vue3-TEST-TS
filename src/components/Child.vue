<template>
  <div>
    <div>{{ 10000 }}</div>
    <button @click="sendValueToParent">sendValueToParent</button>
    <button @click="sendValueToParentFromProps">
      sendValueToParentFromProps
    </button>
    <div>counter:{{ counter }}</div>
  </div>
</template>

<script lang='ts'>
import {
  toRefs,
  defineComponent,
  inject,
  PropType,
} from "vue";
type User = { name: string; password: number };
export default defineComponent({
  name: "",
  props: {
    params: Number,
    data: String,
    showValue: Boolean,
    user: Object as PropType<User>,
    //复杂类型约束使用as PropType<User>断言;
    setShowValue: Function as PropType<(boolean: boolean) => void>,
    //Prop中的数据直接可以在模版引擎中使用,无需使用setup函数进行return暴露;
  },
  setup(props, context) {
    console.log("PROPS_DATA", props);

    console.log("context", context);
    // attrs: (...)
    // emit: (...)
    // expose: (exposed) => {…}
    // slots: (...)
    // get attrs: ƒ attrs()
    // get emit: emit() { return (event, ...args) => {…}
    // get slots: ƒ slots()
    // context中的内容不是响应式的;
    const sendValueToParent = () => {
      context.emit("setShowValue", !props.showValue);
    };
    //调用context中父亲的函数方法,并传入一个参数;

    const sendValueToParentFromProps = () => {
      if (props.setShowValue) props.setShowValue(!props.showValue);
    };
    //调用context中父亲的函数方法,并传入一个参数;

    const { showValue } = toRefs(props);
    console.log("showValue", showValue);
    //如果需要解构Props需要使用toRefs(),此时解构Props中的属性将才会是响应式的;
    //通过props.x也是响应式的;
    //const { showValue } = props;这种情况下数据为非响应式的;

    const childFunction = () => {
      console.log("childFunction执行");
    };

    let counter = inject("counter");
    console.log("inject", inject("counter"));
    //接收从provide注入的响应式数据;

    return {
      sendValueToParent,
      childFunction,
      sendValueToParentFromProps,
      counter,
    };
  },
  beforeCreate() {
    console.log("beforeCreate 调用");
  },
  created() {
    console.log("created 调用");
  },
  beforeMount() {
    console.log("beforeMount 调用");
  },
  mounted() {
    console.log("mounted 调用");
  },
  beforeUpdate() {
    console.log("beforeUpdate 调用");
  },
  updated() {
    console.log("updated 调用");
  },
  beforeUnmount() {
    console.log("beforeUnmount 调用");
  },
  unmounted() {
    console.log("unmounted 调用");
  },
});
</script>
<style scoped>
</style>