<script setup lang="ts">

const isOpen = ref(false)

const links = [{
  label: 'Accueil',
  icon: 'i-heroicons-home',
  to: '/',
}, {
  label: 'Actualités',
  description: 'Communiqués, Annonces, Articles',
  photo: '/unknown_member.webp',
  icon: 'i-heroicons-newspaper',
  to: '/publications/actualites',
},
{
  label: 'Annuaires',
  description: "Gouvernement, Sites Web, Justice...",
  icon: 'i-heroicons-rectangle-stack',
  to: '/annuaires',
},
{
  label: 'Documents',
  description: "Journal officiel, Codes, Rapports OFNAC Cours des comptes...",
  icon: 'i-heroicons-document-text',
  to: '/documents',
}];

const aboutUslinks = [
  {
    label: 'Conseil des ministres',
    description: 'Conseil des ministres',
    photo: '/unknown_member.webp',
    icon: 'i-heroicons-document-text',
    to: '/conseil-des-ministres',
  },
  {
    label: 'Newsletter',
    description: 'Abonnez vous à notre newsletter',
    photo: '/unknown_member.webp',
    icon: 'i-heroicons-envelope',
    to: '/newsletter',
  },
  {
    label: 'Quiz',
    description: 'Jeux QCM sur les institutions publiques',
    photo: '/unknown_member.webp',
    icon: 'i-heroicons-puzzle-piece',
    to: '/quiz',
  },
  {
    label: 'À Propos',
    to: '/about/us',
    icon: 'i-heroicons-information-circle',
  }];


</script>

<template>
  <div class="md:px-10 lg:px-18 xl:px-32 top-header flex justify-between items-center top-0 z-50 sticky opacity-100">
    <!-- HeaderBrand à gauche -->
    <AppHeader />

    <!-- Navigation horizontale pour les écrans plus larges -->
    <UHorizontalNavigation :links="links" class="hidden md:flex items-center w-auto">
    </UHorizontalNavigation>

    <!-- Menu pour mobiles (toggle visibility with Tailwind CSS) -->
    <UButton class="md:hidden" @click="isOpen = true" color="gray" variant="link" size="xl" icon="i-heroicons-bars-3" />
  </div>
  <UContainer class="mt-2 px-0 sm:px-10 md:px-14 lg:px-28 xl:px-40">
    <!-- Navigation verticale pour mobiles (toggle visibility with Tailwind CSS) -->
    <USlideover v-model="isOpen">
      <div class="p-4 flex-1">
        <UButton color="gray" variant="ghost" size="xl" icon="i-heroicons-x-mark-20-solid"
          class="flex sm:hidden absolute end-5 top-5 z-10" square padded @click="isOpen = false" />
        <div class="min-h-full">
          <AppHeader />

          <UVerticalNavigation :links="links" @click="isOpen = false" :ui="{ size: 'text-md' }" class="vertical-nav" />

          <UDivider />

          <UVerticalNavigation :links="aboutUslinks" @click="isOpen = false" :ui="{ size: 'text-md' }"
            class="vertical-nav" />
        </div>
      </div>
    </USlideover>

    <NuxtLoadingIndicator />

    <NuxtLayout>
      <NuxtPage />
    </NuxtLayout>

    <AppFooter />

  </UContainer>
</template>

<style>
.top-header {
  background-color: #f9f9f9;
  /* background-color: #f2f2f2; */
  /* box-shadow: 0 2px 4px 0 rgba(0, 0, 0, 0.05); */
  box-shadow: 0 2px 6px rgba(0, 0, 0, .1);
}

nav ul li a span {
  text-transform: uppercase;
  font-family: "Quicksand", sans-serif;
  text-shadow: 1px 1px 1px rgba(0, 0, 0, 0.004);

  /* font-size: 0.75rem; */

}

.vertical-nav ul li a {
  margin: 0.5rem;
  padding-left: 1rem !important;
  box-shadow: 0 2px 4px #0000001a;
  line-height: 2rem;
}
</style>