<template>
  <header class="bg-white h-20">
    <nav class="h-full flex justify-between container items-center">
      <div>
        <router-link to="/" class="text-ct-dark-600 font-semibold"
          >Sistema caseiro</router-link
        >
      </div>
      <ul class="flex items-center gap-4">
        <li><router-link to="/" class="text-ct-dark-600">Home</router-link></li>
        <li v-if="!user">
          <router-link to="/register" class="text-ct-dark-600"
            >Cadastro</router-link
          >
        </li>
        <li v-if="!user">
          <router-link to="/login" class="text-ct-dark-600">Login</router-link>
        </li>
        <li v-if="user">
          <router-link to="/profile" class="text-ct-dark-600"
            >Perfil</router-link
          >
        </li>
        <li v-if="user" class="cursor-pointer" @click="handleLogout">Sair</li>
      </ul>
    </nav>
  </header>
</template>

<script setup lang="ts">
import { logoutUserFn } from '@/api/authApi';
import { useMutation } from 'vue-query';
import { useAuthStore } from '@/stores/authStore';
import { createToast } from 'mosha-vue-toastify';

const authStore = useAuthStore();

const user = authStore.authUser;

const { mutate: logoutUser } = useMutation(() => logoutUserFn(), {
  onSuccess: () => {
    authStore.setAuthUser(null);
    document.location.href = '/login';
  },
  onError: (error) => {
    if (Array.isArray((error as any).response.data.error)) {
      (error as any).response.data.error.forEach((el: any) =>
        createToast(el.message, {
          position: 'top-right',
          type: 'warning',
        })
      );
    } else {
      createToast((error as any).response.data.message, {
        position: 'top-right',
        type: 'danger',
      });
    }
  },
});

const handleLogout = () => {
  logoutUser();
};
</script>
