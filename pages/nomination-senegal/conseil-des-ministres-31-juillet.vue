<script setup lang="ts">
import type { GovernmentMember } from '~/types/government-member';

useHead({
    title: 'Nominations Conseil des ministres Sénégal du 31 juillet',
    meta: [
        {
            name: 'description',
            content: 'Nominations Conseil des ministres Sénégal du 31 Juillet 2024'
        },
        // Twitter Card Meta Tags
        {
            name: "twitter:title",
            content: "Nominations Conseil des ministres Sénégal | Vie-Publique.sn",
        },
        {
            name: "twitter:description",
            content: "Annuaire des nominations du Conseil des ministres Sénégal du 31 Juillet 2024",
        },
        { name: "twitter:card", content: "summary_large_image" },
        { name: "twitter:image", content: "/nomination-3.png" },
        // Open Graph Meta Tags
        {
            property: "og:title",
            content: "Annuaire nominations Sénégal | Vie-Publique.sn",
        },
        {
            property: "og:description",
            content: "Annuaire des nominations du Conseil des ministres Sénégal du 31 Juillet 2024",
        },
        { property: "og:image", content: "/nomination-3.png" },
        { property: "og:url", content: "https://vie-publique.sn/nomination-senegal/conseil-des-ministres-18-juillet" },
        { property: "og:type", content: "website" },
    ]
})

/* plugin */

const { $dateformat } = useNuxtApp()

/* Get Datas */

const nuxtApp = useNuxtApp()
const { data } = await useFetch('/api/nominations-31-07-2024', {
    watch: false,

    transform(input) {
        return {
            members: input,
            fetchedAt: new Date()
        }
    },

    getCachedData(key) {
        const data = nuxtApp.payload.data[key] || nuxtApp.static.data[key]
        // If data is not fetched yet
        if (!data) {
            // Fetch the first time
            return
        }

        // Is the data too old?
        const expirationDate = new Date(data.fetchedAt)
        expirationDate.setTime(expirationDate.getTime() + 120 * 1000) // 120 secondes
        const isExpired = expirationDate.getTime() < Date.now()
        if (isExpired) {
            // Refetch the data
            return
        }

        return data
    },
})

/* Filters */

const searchQuery = ref('');
const selectedType = ref('');
const selectedGender = ref('');
const selectedDate = ref('');

const filteredMinisters = computed(() => {
    return (data.value.members?.filter((member: GovernmentMember) =>
        (member.name.toLowerCase().includes(searchQuery.value.toLowerCase())
            || member.role.toLowerCase().includes(searchQuery.value.toLowerCase())) &&
        (!selectedType.value || member.type === selectedType.value) &&
        (!selectedGender.value || member.sexe === selectedGender.value) &&
        (!selectedDate.value || member.sexe === selectedDate.value)
    ) || []).sort((a, b) => new Date(b.nominationDate).getTime() - new Date(a.nominationDate).getTime());
});

// FIXME move to server
const totalsByType = computed(() => {
    const totals: Record<string, number> = {};
    data.value.members?.forEach((member: GovernmentMember) => {
        if (member.type) {
            if (!totals[member.type]) {
                totals[member.type] = 0;
            }
            totals[member.type]++;
        }
    });

    // Convert object to array, sort alphabetically, then convert back to object
    return Object.fromEntries(
        Object.entries(totals).sort(([keyA], [keyB]) => keyA.localeCompare(keyB))
    );

    // return totals;
});

// FIXME move to server
const totalsByGender = computed(() => {
    let maleCount = 0;
    let femaleCount = 0;
    data.value.members?.forEach((member: GovernmentMember) => {
        if (member.sexe === 'Monsieur') {
            maleCount++;
        } else if (member.sexe === 'Madame') {
            femaleCount++;
        }
    });
    return { maleCount, femaleCount };
});

const selectedMinister = ref<GovernmentMember | null>(null);
const isModalOpen = ref(false);

function openModal(minister: GovernmentMember) {
    selectedMinister.value = minister;
    isModalOpen.value = true;
}

function filterByGender(gender: string) {
    selectedGender.value = gender;
}

</script>

<template>

    <div class="flex flex-col items-center px-4">
        <h1 class="text-sm mb-4 text-gray-500 sr-only">
            Membres du gouvernement du Sénégal, Nouveau gouvernement Sénégal Diomaye Sonko,
            Conseil des ministres, Liste des ministres du Sénégal,
        </h1>
        <div class="prose prose-sm sm:prose mx-auto mt-2">
            <h1 class="text-center mt-4 mb-2">
                {{ data.members?.length }} Nominations au conseil des ministres du 31 Juillet 2024
            </h1>
        </div>

        <!-- Modal pour afficher les détails du membre -->
        <UModal v-model="isModalOpen">
            <UCard v-if="selectedMinister" :ui="{ ring: '', divide: 'divide-y divide-gray-100 dark:divide-gray-800' }">
                <template #header>
                    <!-- <Placeholder v-else class="h-8" /> -->
                    <div class="flex justify-center items-center">
                        <NuxtImg :src="selectedMinister.photo || '/unknown_member.webp'" alt="Profile Photo"
                            sizes="300px" class="h-auto" :placeholder="[300, 300]" />
                    </div>
                </template>

                <div class="p-4 text-center">
                    <h2 class="text-xl font-semibold">{{ selectedMinister.name }}</h2>
                    <p class="text-sm">{{ selectedMinister.role }}</p>
                    <div v-if="selectedMinister.nominationDate != null" class="mt-1">
                        <p class="text-sm text-gray-500">Nommé le</p>
                        <p class="text-sm">{{ $dateformat(selectedMinister.nominationDate) }}</p>
                    </div>
                    <div v-if="selectedMinister.formation != null" class="mt-1">
                        <p class="text-sm text-gray-500">Formation</p>
                        <p class="text-sm">{{ selectedMinister.formation }}</p>
                    </div>
                    <div v-if="selectedMinister.predecessor != null" class="mt-1">
                        <p class="text-sm text-gray-500">Prédécesseur</p>
                        <p class="text-sm">{{ selectedMinister.predecessor }}</p>
                    </div>
                </div>

                <template #footer>
                    <Placeholder class="h-8" />
                    <div class="text-right p-4">
                        <button class="btn btn-primary" @click="isModalOpen = false">Fermer</button>
                    </div>
                </template>
            </UCard>
        </UModal>

        <div class="w-full max-w-4xl">
            <UInput class="input w-full mb-3 custom-shadow" size="lg" icon="i-heroicons-magnifying-glass"
                placeholder="Rechercher une nomination..." v-model="searchQuery">
            </UInput>

            <div class="text-center w-full mb-1">
                <UButton :ui="{ rounded: 'rounded-full' }" class="text-sm custom-shadow ml-1 mb-1 font-normal"
                    :color="selectedGender === 'Monsieur' ? 'primary' : 'white'"
                    @click="selectedGender = selectedGender === 'Monsieur' ? '' : 'Monsieur'" size="sm">
                    Hommes
                    <UBadge :ui="{ rounded: 'rounded-full' }" :label="totalsByGender.maleCount"
                        :color="selectedGender === 'Monsieur' ? 'primary' : 'primary'"
                        :variant="selectedGender === 'Monsieur' ? 'soft' : 'solid'" size="xs"></UBadge>
                </UButton>
                <UButton :ui="{ rounded: 'rounded-full' }" class="text-sm custom-shadow ml-1 mb-1 font-normal"
                    :color="selectedGender === 'Madame' ? 'primary' : 'white'"
                    @click="selectedGender = selectedGender === 'Madame' ? '' : 'Madame'" size="sm">
                    Femmes
                    <UBadge :ui="{ rounded: 'rounded-full' }" :label="totalsByGender.femaleCount" color="primary"
                        :variant="selectedGender === 'Madame' ? 'soft' : 'solid'" size="xs"></UBadge>
                </UButton>
            </div>

            <div class="text-center w-full mb-1">

                <UButton v-for="(total, type) in totalsByType" :key="type" :ui="{ rounded: 'rounded-full' }"
                    :color="selectedType === type ? 'primary' : 'white'"
                    class="text-sm custom-shadow ml-1 mb-1 font-normal"
                    @click="selectedType = selectedType === type ? '' : type" size="sm">
                    {{ type }}
                    <UBadge :ui="{ rounded: 'rounded-full' }" :label="total" color="primary"
                        :variant="selectedType === type ? 'soft' : 'solid'" size="xs"></UBadge>
                </UButton>
            </div>

            <div class="text-center w-full mb-3">
                <NuxtLink to="/nomination-senegal" class="text-sm mb-2 underline text-center">
                    Voir la totalité des 277 nominations
                </NuxtLink>
            </div>

            <div class="space-y-2">
                <UCard v-for="minister in filteredMinisters" :key="minister.name" class="cursor-pointer custom-shadow"
                    @click="openModal(minister)">
                    <div class="flex flex-row gap-2 ">
                        <div class="flex-shrink-0 w-16 h-16 md:w-20 md:h-20">
                            <NuxtImg :src="minister.photo || '/unknown_member.webp'" alt="Photo ministre"
                                sizes="64px sm:80px" class="object-cover rounded-full w-full h-full" placeholder />


                        </div>
                        <div class="flex-grow">
                            <h2 class="font-semibold">{{ minister.name }}</h2>
                            <p class="text-sm">{{ minister.role }}</p>
                            <p class="text-sm text-gray-500">Nommé le
                                {{ $dateformat(minister.nominationDate) }}
                            </p>
                            <p v-if="minister.endDate != null" class="text-sm text-gray-500">Limogé le
                                {{ $dateformat(minister.endDate) }}
                            </p>
                        </div>
                    </div>
                </UCard>
            </div>
        </div>

    </div>
</template>

<style scoped>
h1 {
    font-size: 24px;
    margin-bottom: 10px;
}
</style>