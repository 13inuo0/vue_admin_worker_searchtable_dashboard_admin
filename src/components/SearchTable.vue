<template>
  <div class="w-full">
    <!-- 검색 및 필터 영역 -->
    <div class="bg-white dark:bg-gray-800 rounded-lg shadow p-4 mb-6">
      <div class="flex flex-col md:flex-row gap-4">
        <!-- 검색 입력 -->
        <div class="flex-1">
          <div class="relative">
            <input
              v-model="localSearchQuery"
              type="text"
              :placeholder="searchPlaceholder"
              class="w-full pl-10 pr-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 bg-white dark:bg-gray-700 text-gray-900 dark:text-white"
            />
            <i class="fas fa-search absolute left-3 top-3 text-gray-400 dark:text-gray-500"></i>
          </div>
        </div>
        <!-- 필터 선택 (있는 경우만 표시) -->
        <div class="flex gap-2" v-if="filterOptions && filterOptions.length > 0">
          <select
            v-for="(filter, index) in filterOptions"
            :key="index"
            v-model="localFilters[filter.key]"
            @change="handleFilterChange"
            class="border border-gray-300 dark:border-gray-600 rounded-lg px-4 py-2 focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 bg-white dark:bg-gray-700 text-gray-900 dark:text-white"
          >
            <option v-for="option in filter.options" :key="option.value" :value="option.value">
              {{ option.label }}
            </option>
          </select>
        </div>
      </div>
    </div>
    <!-- 테이블영역 -->
    <div class="bg-white dark:bg-gray-800 rounded-lg shadow">
      <div v-if="tableTitle" class="p-4 border-b border-gray-200 dark:border-gray-700">
        <h2 class="text-lg font-semibold text-gray-800 dark:text-white">
          {{ tableTitle }}
        </h2>
      </div>
      <!-- 테이블 목록 -->
      <div class="overflow-x-auto">
        <table class="min-w-full divide-y divide-gray-200 dark:divide-gray-700">
          <thead class="bg-gray-50 dark:bg-gray-700">
            <tr>
              <th
                v-for="column in columns"
                :key="column.key"
                class="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider"
              >
                {{ column.label }}
              </th>
            </tr>
          </thead>
        </table>
      </div>
    </div>
    <!-- 페이지 네이션 영역 -->
    <div class="flex justify-between items-center bg-white dark:bg-gray-800 rounded-lg shadow p-4 mt-6">
      <div class="text-sm text-gray-700 dark:text-gray-300">
        총 <span>{{ filteredData.length }}</span>
      </div>
    </div>
  </div>
</template>
<script setup>
import { ref, watch, computed } from "vue";
// 로컬 상태

const localSearchQuery = ref("");
const localFilters = ref({});
// Props 정의
const props = defineProps({
  // 전체 데이터 배열
  data: {
    type: Array,
    required: true,
    default: () => [],
  },
  // 검색 플레이스홀더
  searchPlaceholder: {
    type: String,
    default: "검색...",
  },
  //   필터 옵션
  filterOptions: {
    type: Array,
    default: () => [],
  },
  //   검색할 필드명 배열
  searchFields: {
    type: Array,
    default: () => [],
  },

  // 테이블 제목
  tableTitle: {
    type: String,
    default: "",
  },
  // 테이블 컬럼 정의
  columns: {
    type: Array,
    required: true,
  },
});

// 필터 초기화
//  만약에 필터 옵션이 있고 그안에 내용이 있다면
if (props.filterOptions && props.filterOptions.length > 0) {
  // 각 필터를 하나씩 꺼내서
  props.filterOptions.forEach((filter) => {
    // localFilters.value[filter.key] =
    console.log(filter);
    // ?기능은 옵션이 있으면 첫번째 값 넣어주고 없으면 빈 문자열을 넣어준다.
    localFilters.value[filter.key] = filter.options[0]?.value || "";
  });
}
// 화면에 보여줄 데이터를 계산하는곳
const filteredData = computed(() => {
  // props.data를 그대로 복사해서 result에 저장
  let result = [...props.data];
  // 검색창에 글자가 있고, 검색할 필드가 정해져 잇으면 실행
  if (localSearchQuery.value && props.searchFields.length > 0) {
    // 입력한 글자를 모두 소문자로 바꿔서 저장
    const query = localSearchQuery.value.toLowerCase();
    // reslt 배열에서 조건에 맞는 데이터만 남기기
    result = result.filter((item) => {
      console.log(item);

      // some()배열에서 주어진 함수를 만족하는 첫 번째 요소를 반환하는 메서드
      return props.searchFields.some((field) => {
        // console.log(field);

        const value = item[field]; //고객이름, 예약번호
        console.log(value);

        return value && value.toString().toLowerCase().includes(query);
      });
    });
  }
  //   다 필터링한 결과를 반환
  return result;
});
// 데이터 변경시
watch(() => props.data, { deep: true });
</script>
