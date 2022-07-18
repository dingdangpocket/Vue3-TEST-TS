<template>
  <div>
    <Child
      :params="count"
      data="data"
      :showValue="showValue"
      :user="user"
      :setShowValue="setShowValue"
      @setShowValue="setShowValue"
      ref="childref"
    />
    <div>{{ showValue }}</div>
    <div>{{ count }}</div>
    <button @click="count++">increment</button>
  </div>
</template>

<script lang='ts'>
import {
  reactive,
  ref,
  onMounted,
  h,
  watchEffect,
  defineComponent,
  computed,
  watch,
  provide,
  isRef,
  isReactive,
  toRaw,
} from "vue";
import Child from "./components/Child.vue";

interface DataProps {
  key: string;
}
export default defineComponent({
  name: "",
  components: { Child },
  props: {
    name: String,
    //可直接交予模板引擎使用;
  },
  beforeRouteEnter(to: any, from: any, next: () => void) {
    next();
    // 进入路由前调用;
  },
  beforeRouteLeave(to: any, from: any, next: () => void) {
    next();
    //离开路由前调用;
  },
  setup(props, context) {
    const childref = ref();
    //引用组件实例;
    const count = ref(0);
    const key = ref(1);
    const age = ref(2);
    const showValue = ref(true);
    //ref响应数据state(基本类型);
    const user = reactive({
      name: "JueJin",
      password: 3,
    });
    const object = reactive({ key: "point" });
    const state = reactive({
      name: "JueJin",
      age: 3,
    });
    //reactive响应数据定义(引用类型数据);
    console.log("访问响应数据", count.value, state.name);

    const setShowValue = (value: boolean) => {
      //接收来自子组件的value值;
      showValue.value = value;
    };
    //定义一个函数方法;

    onMounted(() => {
      childref.value.childFunction();
      //父组件引用子组件的方法;
    });

    watchEffect(() => {
      console.log(`监听整个Props变化的Hook` + props.name);
    });

    let addSum = computed(() => {
      return key.value + age.value;
    });
    console.log("计算结果", addSum);
    //初始触发、数据变化后再次触发计算属性返回给变量;

    watch(
      key,
      (newValue, oldValue) => {
        console.log(`a从${oldValue}变成了${newValue}`);
      },
      { deep: true }
    );
    //监听某一个ref定义的响应式数据;

    watch([key, age], (newValue, oldValue) => {
      console.log("key或age变化", newValue, oldValue);
    });
    //监听多个ref定义的响应式数据;

    watch(
      state,
      (newValue, oldValue) => {
        console.log("state变化了", newValue, oldValue);
      },
      { immediate: true }
    );
    //监听reactive定义的响应式数据;

    watch(
      () => state.age,
      (newValue, oldValue) => {
        console.log("state的age变化", newValue, oldValue);
      },
      { immediate: true, deep: true }
    );
    //监听reactive定义的响应式数据中的某个属性;

    watch(
      [() => state.age, () => state.name],
      (newValue, oldValue) => {
        console.log("state的age、name变化", newValue, oldValue);
      },
      { immediate: true, deep: true }
    );
    //监听reactive定义的响应式数据中的某些属性

    watchEffect(() => {
      const x1 = state.age;
      const x2 = state.name;
      console.log("watchEffect配置的回调执行", x1, x2);
    });
    //watchEffect所指定的回调中“使用”到的数据只要发生变化，就会重新执行回调。

    console.log(isRef(key)); // true
    console.log(isReactive(state.age)); // true
    //检测数据是否是响应式;

    const rawKey = toRaw(key);
    console.log("rawKey", rawKey);
    //转为非响应式数据;

    const renderComp = () => {
      return h("H1", [count.value, object.key]);
    };
    //渲染函数需要使用h函数进行渲染,再return中返回一个回掉函数渲染;

    provide("counter", count);
    //父组件提供一个响应式的上下文state,子组件通过inject接收;

    return { count, setShowValue, showValue, renderComp, childref, user };
  },
});
</script>
<style scoped>
</style>