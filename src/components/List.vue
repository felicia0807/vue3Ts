<template>
  <div>
    <div
      v-for="(item, index) in list"
      :key="index"
      :style="{
        background: item.background,
        height: '500px',
        width: '100%',
        display: 'flex',
        alignItems: 'center',
        justifyContent: 'center',
      }"
    >
      {{ index }}
    </div>
    <b-spinner v-if="loading" class="loading"> </b-spinner>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, onMounted } from "vue";

interface ListItem {
  background: string;
}

export default defineComponent({
  name: "List",
  setup() {
    const list = ref<ListItem[]>([{ background: "rgb(233,32,38)" }]);
    const loading = ref(false);
    const maxItems = 50;

    const getList = async (num: number): Promise<ListItem[]> => {
      const delay = Math.random() * 5 * 1000;
      await new Promise((resolve) => setTimeout(resolve, delay));
      return Array.from({ length: num }, () => ({
        background: `rgb(${Math.floor(Math.random() * 256)}, ${Math.floor(
          Math.random() * 256
        )}, ${Math.floor(Math.random() * 256)})`,
      }));
    };

    const loadMore = async () => {
      if (list.value.length >= maxItems || loading.value) return;
      loading.value = true;
      const newItems = await getList(10);
      list.value = [...list.value, ...newItems];
      loading.value = false;
    };

    const handleScroll = () => {
      const scrollTop = document.documentElement.scrollTop;
      const windowHeight = window.innerHeight;
      const scrollHeight = document.documentElement.scrollHeight;

      if (scrollTop + windowHeight >= scrollHeight / 2) {
        loadMore();
      }
    };

    onMounted(async () => {
      await loadMore(); // Load initial data
      window.addEventListener("scroll", handleScroll);
    });

    return {
      list,
      loading,
    };
  },
});
</script>

<style scoped>
.loading {
  width: 48px;
  height: 48px;
  border: 5px solid #000;
  border-bottom-color: transparent;
  border-radius: 50%;
  display: inline-block;
  box-sizing: border-box;
  animation: rotation 1s linear infinite;
  margin-top: 20px;
}

@keyframes rotation {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
</style>
