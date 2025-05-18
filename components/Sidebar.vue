<template>
  <aside
    :class="[
      'sidebar border-r ring-default transition-all duration-300',
      isCollapsed ? 'w-[70px] min-w-[70px]' : 'w-[250px] min-w-[250px]',
    ]"
  >
    <div class="flex flex-col h-full grow">
      <!-- Logo -->
      <div class="text-center" :class="isCollapsed ? 'px-3 py-3' : 'px-4 py-6'">
        <NuxtImg
          src="/logo.svg"
          alt="Logo"
          class="mx-auto"
          :class="isCollapsed ? 'max-h-10' : 'max-h-14'"
        />
      </div>

      <!-- Menu -->
      <div class="flex-1 overflow-y-auto">
        <UNavigationMenu
          orientation="vertical"
          class="px-2"
          :items="navLinks"
          :collapsed="isCollapsed"
          :ui
        />
      </div>

      <!-- Bottom Controls -->
      <div class="border-t ring-default py-2">
        <!-- Toggle Theme Button -->
        <div class="mb-2 flex justify-center">
          <UButton
            :icon="
              $colorMode.value === 'light'
                ? 'i-heroicons-moon'
                : 'i-heroicons-sun'
            "
            variant="ghost"
            color="gray"
            aria-label="Toggle theme"
            @click="toggleColorMode"
          />
          <!-- <select class="ring-default text-sm" v-model="$colorMode.preference">
            <option value="system">System</option>
            <option value="light">Light</option>
            <option value="dark">Dark</option>
            <option value="sepia">Sepia</option>
          </select> -->
        </div>

        <!-- Toggle Sidebar Button -->
        <UButton
          block
          variant="soft"
          color="gray"
          :icon="
            isCollapsed ? 'i-heroicons-arrow-right' : 'i-heroicons-arrow-left'
          "
          size="sm"
          @click="toggleSidebar"
        >
          <span v-if="!isCollapsed">Collapse</span>
        </UButton>
      </div>
    </div>
  </aside>
</template>

<script setup lang="ts">
// У меня началась тряска из-за постоянной перезагрузки меню, поэтому переписал блок с запросом
// Спасбио за понимание
// Комменатрии, которые я не оставлял бы в проде (для ревьюеров по сути) отметил с помощью NP:

const config = useRuntimeConfig();
const wpApiUrl = config.public.wordpressApiUrl;

// TODO: interface type, proper mocks
type TNavLink = { post_title: string; icon: string; url: string };
const cvPage: TNavLink = {
  icon: "i-heroicons-document-text",
  post_title: "Резюме",
  url: "/cv",
};

const isCollapsed = defineModel<boolean>("isCollapsed", { default: false });
// NP: Лучше использовать reactive([]) вместо обычного [], т.к reactive будет реагировать на изменения внутренних reactive и на .push
const menu = ref<TNavLink[]>(reactive([]));
const error = ref<string | null>(null);
const $colorMode = useColorMode();

// Navigation links computed from WP menu items
const navLinks = computed(() => {
  if (error.value) {
    return [
      {
        label: "Error loading menu",
        icon: "i-heroicons-exclamation-triangle",
        to: "#",
      },
    ];
  }

  if (menu.value.length === 0) {
    return [{ label: "Loading...", icon: "i-heroicons-arrow-path", to: "#" }];
  }

  return menu.value.map((item) => {
    return {
      label: item.post_title,
      icon: item.icon,
      to: `${item.url}`,
    };
  });
});

const ui = computed(() => {
  if (isCollapsed.value) {
    return {
      linkLeadingIcon: "text-2xl m-auto",
    };
  }
  return { linkLeadingIcon: "text-lg" };
});

// Function to toggle sidebar collapse state
const toggleSidebar = () => {
  isCollapsed.value = !isCollapsed.value;
};

// Function to toggle theme
function toggleColorMode() {
  $colorMode.preference = $colorMode.value === "dark" ? "light" : "dark";
}
try {
  // Using standard WP REST API to get pages as menu items
  const { data, error: restError } = await useFetch<TNavLink[]>(
    () => `${wpApiUrl}/menus/87`,
    { key: "sidebar-nav" }
  );
  if (restError.value || !data.value) {
    throw new Error("Failed to fetch menu data");
  }
  menu.value = reactive(data.value) || [];
  if (menu.value.length === data.value.length) {
    menu.value.push(cvPage);
  }
} catch (err) {
  console.error("Error fetching menu:", err);
  error.value = "Failed to load menu items";
}
</script>

<style scoped>
.sidebar {
  box-shadow: 0 0 15px rgba(0, 0, 0, 0.1);
}
</style>
