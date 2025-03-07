<script setup>
import { shorten } from '@/helpers/utils';
import { useInfiniteScroll } from '@vueuse/core';

const { orderedSpacesByCategory, spacesLoaded } = useSpaces();
const { formatCompactNumber } = useIntl();

const loadBy = 12;
const limit = ref(loadBy);

const enableInfiniteScroll = ref(false);

const loadMoreSpaces = () => {
  enableInfiniteScroll.value = true;
  limit.value += loadBy;
};

useInfiniteScroll(
  document,
  () => {
    if (enableInfiniteScroll.value) {
      limit.value += loadBy;
    }
  },
  { distance: 400 }
);
</script>

<template>
  <div class="relative">
    <BaseContainer
      class="mb-4 flex flex-col flex-wrap items-center xs:flex-row md:flex-nowrap"
    >
      <BaseButton
        tabindex="-1"
        class="w-full pl-3 pr-0 focus-within:!border-skin-link md:max-w-[420px]"
      >
        <TheSearchBar />
      </BaseButton>

      <ExploreMenuCategories />

      <div
        v-if="spacesLoaded"
        class="mt-2 whitespace-nowrap text-right text-skin-text xs:ml-auto xs:mt-0"
      >
        {{
          $tc('spaceCount', [
            formatCompactNumber(orderedSpacesByCategory.length)
          ])
        }}
      </div>
    </BaseContainer>

    <BaseContainer :slim="true">
      <TransitionGroup
        name="fade"
        tag="div"
        class="grid gap-4 md:grid-cols-3 lg:grid-cols-4"
      >
        <div
          v-for="space in orderedSpacesByCategory.slice(0, limit)"
          :key="space.id"
        >
          <router-link
            :to="{ name: 'spaceProposals', params: { key: space.id } }"
          >
            <BaseBlock
              class="mb-0 flex items-center justify-center text-center transition-all hover:border-skin-text"
              style="height: 266px"
            >
              <div class="relative mb-2 inline-block">
                <AvatarSpace
                  :space="space"
                  symbol-index="space"
                  size="82"
                  class="mb-1"
                />
              </div>
              <div class="flex items-center justify-center gap-1 truncate">
                <h3
                  class="mb-0 mt-0 !h-[32px] overflow-hidden pb-0 text-[22px]"
                  v-text="shorten(space.name, 16)"
                />
                <IconVerifiedSpace :space-id="space.id" class="pt-[1px]" />
              </div>
              <div class="mb-[12px] text-skin-text">
                {{
                  $tc('members', space.followers, {
                    count: formatCompactNumber(space.followers)
                  })
                }}
              </div>
              <ButtonFollow class="!mb-0" :space="space" />
            </BaseBlock>
          </router-link>
        </div>
      </TransitionGroup>
      <ExploreSkeletonLoading v-if="!spacesLoaded" is-spaces />
      <BaseNoResults
        v-else-if="Object.keys(orderedSpacesByCategory).length < 1"
        use-block
      />
      <div class="px-4 text-center md:px-0">
        <BaseButton
          v-if="!enableInfiniteScroll && orderedSpacesByCategory.length > limit"
          class="mt-4 w-full"
          @click="loadMoreSpaces()"
        >
          {{ $t('homeLoadmore') }}
        </BaseButton>
      </div>
    </BaseContainer>
  </div>
</template>
