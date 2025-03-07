<script lang="ts" setup>
import type { Context } from '@/api/types'
import TorrentItem from '@/components/cards/TorrentItem.vue'

// 定义输入参数
const props = defineProps({
  // 数据列表
  items: Array as PropType<Context[]>,
})

// 过滤表单
const filterForm = reactive({
  // 站点
  site: [] as string[],
  // 季
  season: [] as string[],
  // 制作组
  releaseGroup: [] as string[],
  // 视频编码
  videoCode: [] as string[],
  // 促销状态
  freeState: [] as string[],
  // 质量
  edition: [] as string[],
  // 分辨率
  resolution: [] as string[],
})

// 数据列表
const dataList = ref <Array<Context>>([])

// 获取站点过滤选项
const siteFilterOptions = ref<Array<string>>([])
// 获取季过滤选项
const seasonFilterOptions = ref<Array<string>>([])
// 获取制作组过滤选项
const releaseGroupFilterOptions = ref<Array<string>>([])
// 获取视频编码过滤选项
const videoCodeFilterOptions = ref<Array<string>>([])
// 获取促销状态过滤选项
const freeStateFilterOptions = ref<Array<string>>([])
// 获取质量过滤选项
const editionFilterOptions = ref<Array<string>>([])
// 获取分辨率过滤选项
const resolutionFilterOptions = ref<Array<string>>([])

// 初始化过滤选项
function initOptions(data: Context) {
  const { torrent_info, meta_info } = data
  const optionValue = (options: Array<string>, value: string | undefined) => {
    value && !options.includes(value) && options.push(value)
  }
  optionValue(siteFilterOptions.value, torrent_info?.site_name)
  optionValue(seasonFilterOptions.value, meta_info?.season_episode)
  optionValue(releaseGroupFilterOptions.value, meta_info?.resource_team)
  optionValue(videoCodeFilterOptions.value, meta_info?.video_encode)
  optionValue(freeStateFilterOptions.value, torrent_info?.volume_factor)
  optionValue(editionFilterOptions.value, meta_info?.edition)
  optionValue(resolutionFilterOptions.value, meta_info?.resource_pix)
}

// 计算过滤后的列表
watchEffect(() => {
  // 清空列表
  dataList.value.splice(0)
  // 匹配过滤函数
  const match = (filter: Array<string>, value: string | undefined) =>
    filter.length === 0 || (value && filter.includes(value))

  props.items?.forEach((data) => {
    const { meta_info, torrent_info } = data
    if (
      // 站点过滤
      match(filterForm.site, torrent_info.site_name)
        // 促销状态过滤
        && match(filterForm.freeState, torrent_info.volume_factor)
        // 季过滤
        && match(filterForm.season, meta_info.season_episode)
        // 制作组过滤
        && match(filterForm.releaseGroup, meta_info.resource_team)
        // 视频编码过滤
        && match(filterForm.videoCode, meta_info.video_encode)
        // 分辨率过滤
        && match(filterForm.resolution, meta_info.resource_pix)
        // 质量过滤
        && match(filterForm.edition, meta_info.edition)
    )
      dataList.value.push(data)
  })
})

// 初始化过滤选项
onMounted(() => {
  props.items?.forEach((item) => {
    initOptions(item)
  })
})
</script>

<template>
  <VRow>
    <VCol>
      <VList
        lines="three"
        class="rounded"
      >
        <TorrentItem
          v-for="(item, index) in dataList"
          :key="`${index}_${item.torrent_info.title}_${item.torrent_info.site}`"
          :torrent="item"
        />
        <VListItem v-if="dataList.length === 0">
          <VListItemTitle>没有附合当前过滤条件的资源。</VListItemTitle>
        </VListItem>
      </VList>
    </VCol>
    <VCol
      xl="2"
      md="3"
      class="d-none d-md-block"
    >
      <VList lines="one" class="rounded">
        <VListSubheader v-if="siteFilterOptions.length > 0">
          站点
        </VListSubheader>
        <VListItem>
          <VChipGroup v-model="filterForm.site" column multiple>
            <VChip
              v-for="site in siteFilterOptions"
              :key="site"
              :color="filterForm.site.includes(site) ? 'primary' : ''"
              filter
              variant="outlined"
              :value="site"
            >
              {{ site }}
            </VChip>
          </VChipGroup>
        </VListItem>
        <VListSubheader v-if="editionFilterOptions.length > 0">
          质量
        </VListSubheader>
        <VListItem>
          <VChipGroup v-model="filterForm.edition" column multiple>
            <VChip
              v-for="edition in editionFilterOptions"
              :key="edition"
              :color="filterForm.edition.includes(edition) ? 'primary' : ''"
              filter
              variant="outlined"
              :value="edition"
            >
              {{ edition }}
            </VChip>
          </VChipGroup>
        </VListItem>
        <VListSubheader v-if="resolutionFilterOptions.length > 0">
          分辨率
        </VListSubheader>
        <VListItem>
          <VChipGroup v-model="filterForm.resolution" column multiple>
            <VChip
              v-for="resolution in resolutionFilterOptions"
              :key="resolution"
              :color="filterForm.resolution.includes(resolution) ? 'primary' : ''"
              filter
              variant="outlined"
              :value="resolution"
            >
              {{ resolution }}
            </VChip>
          </VChipGroup>
        </VListItem>
        <VListSubheader v-if="releaseGroupFilterOptions.length > 0">
          制作组
        </VListSubheader>
        <VListItem>
          <VChipGroup v-model="filterForm.releaseGroup" column multiple>
            <VChip
              v-for="releaseGroup in releaseGroupFilterOptions"
              :key="releaseGroup"
              :color="filterForm.releaseGroup.includes(releaseGroup) ? 'primary' : ''"
              filter
              variant="outlined"
              :value="releaseGroup"
            >
              {{ releaseGroup }}
            </VChip>
          </VChipGroup>
        </VListItem>
        <VListSubheader v-if="videoCodeFilterOptions.length > 0">
          视频编码
        </VListSubheader>
        <VListItem>
          <VChipGroup v-model="filterForm.videoCode" column multiple>
            <VChip
              v-for="videoCode in videoCodeFilterOptions"
              :key="videoCode"
              :color="filterForm.videoCode.includes(videoCode) ? 'primary' : ''"
              filter
              variant="outlined"
              :value="videoCode"
            >
              {{ videoCode }}
            </VChip>
          </VChipGroup>
        </VListItem>
        <VListSubheader v-if="freeStateFilterOptions.length > 0">
          促销状态
        </VListSubheader>
        <VListItem>
          <VChipGroup v-model="filterForm.freeState" column multiple>
            <VChip
              v-for="freeState in freeStateFilterOptions"
              :key="freeState"
              :color="filterForm.freeState.includes(freeState) ? 'primary' : ''"
              filter
              variant="outlined"
              :value="freeState"
            >
              {{ freeState }}
            </VChip>
          </VChipGroup>
        </VListItem>
        <VListSubheader v-if="seasonFilterOptions.length > 0">
          季集
        </VListSubheader>
        <VListItem>
          <VChipGroup v-model="filterForm.season" column multiple>
            <VChip
              v-for="season in seasonFilterOptions"
              :key="season"
              :color="filterForm.season.includes(season) ? 'primary' : ''"
              filter
              variant="outlined"
              :value="season"
            >
              {{ season }}
            </VChip>
          </VChipGroup>
        </VListItem>
      </VList>
    </VCol>
  </VRow>
</template>
