<template>
  <div class="min-h-screen bg-gray-50 font-sans text-gray-800">
    <!-- 頂部導覽列 -->
    <header class="bg-gradient-to-r from-emerald-600 to-teal-600 text-white shadow-md">
      <div class="max-w-7xl mx-auto px-4 py-4 flex justify-between items-center">
        <div class="flex items-center space-x-2">
          <span class="text-2xl">🌱</span>
          <h1 class="text-xl font-bold tracking-wide">SmartSpend 永續綠色記帳本</h1>
        </div>
        <p class="text-sm bg-emerald-700/50 px-3 py-1 rounded-full backdrop-blur-sm">
          2026 年度個人財務與碳排管理
        </p>
      </div>
    </header>

    <!-- 主要內容區 -->
    <main class="max-w-7xl mx-auto px-4 py-8 grid grid-cols-1 lg:grid-cols-3 gap-8">
      
      <!-- 左側與中間：數據看板 & 記帳清單 -->
      <div class="lg:col-span-2 space-y-8">
        
        <!-- 數據卡片區 -->
        <div class="grid grid-cols-1 sm:grid-cols-3 gap-4">
          <div class="bg-white p-6 rounded-2xl shadow-sm border border-gray-100 flex flex-col justify-between">
            <span class="text-gray-500 text-sm font-medium">總累積花費</span>
            <span class="text-2xl font-bold text-gray-900 mt-2">${{ totalExpense.toLocaleString() }}</span>
            <span class="text-xs text-gray-400 mt-1">本月預算剩餘 45%</span>
          </div>
          
          <div class="bg-white p-6 rounded-2xl shadow-sm border border-gray-100 flex flex-col justify-between">
            <span class="text-gray-500 text-sm font-medium">估計碳排放量</span>
            <span class="text-2xl font-bold text-emerald-600 mt-2">{{ totalCarbon.toFixed(1) }} <span class="text-sm font-normal text-gray-500">kg CO₂e</span></span>
            <span class="text-xs text-emerald-600 mt-1">優於全國平均 12%</span>
          </div>

          <div class="bg-white p-6 rounded-2xl shadow-sm border border-gray-100 flex flex-col justify-between">
            <span class="text-gray-500 text-sm font-medium">綠色消費比例</span>
            <span class="text-2xl font-bold text-teal-600 mt-2">{{ greenRatio }}%</span>
            <span class="text-xs text-gray-400 mt-1">環保低碳的日常消費</span>
          </div>
        </div>

        <!-- 圓餅圖/比例視覺化 (純 SVG 打造，輕量化) -->
        <div class="bg-white p-6 rounded-2xl shadow-sm border border-gray-100">
          <h3 class="text-lg font-bold text-gray-900 mb-4">消費碳足跡類別佔比</h3>
          <div class="flex flex-col sm:flex-row items-center justify-around p-4">
            <!-- 簡單的環狀比例展示 -->
            <div class="relative w-40 h-40">
              <svg viewBox="0 0 36 36" class="w-full h-full transform -rotate-90">
                <path class="text-gray-100" stroke-width="3" stroke="currentColor" fill="none" d="M18 2.0845 a 15.9155 15.9155 0 0 1 0 31.831 a 15.9155 15.9155 0 0 1 0 -31.831" />
                <path class="text-emerald-500" :stroke-dasharray="`${greenRatio}, 100`" stroke-width="3" stroke-linecap="round" stroke="currentColor" fill="none" d="M18 2.0845 a 15.9155 15.9155 0 0 1 0 31.831 a 15.9155 15.9155 0 0 1 0 -31.831" />
              </svg>
              <div class="absolute inset-0 flex flex-col items-center justify-center">
                <span class="text-2xl font-extrabold text-gray-800">{{ greenRatio }}%</span>
                <span class="text-xs text-gray-400">環保消費</span>
              </div>
            </div>
            <!-- 圖例 -->
            <div class="space-y-3 mt-4 sm:mt-0">
              <div class="flex items-center space-x-2">
                <span class="w-3 h-3 bg-emerald-500 rounded-full"></span>
                <span class="text-sm text-gray-600">低碳環保（蔬食、大眾運輸等）</span>
              </div>
              <div class="flex items-center space-x-2">
                <span class="w-3 h-3 bg-gray-300 rounded-full"></span>
                <span class="text-sm text-gray-600">一般排碳（肉食、燃油交通、購物）</span>
              </div>
            </div>
          </div>
        </div>

        <!-- 歷史記帳清單 -->
        <div class="bg-white rounded-2xl shadow-sm border border-gray-100 overflow-hidden">
          <div class="px-6 py-4 border-b border-gray-100 flex justify-between items-center">
            <h3 class="text-lg font-bold text-gray-900">今日消費明細</h3>
            <span class="text-xs font-semibold text-gray-500">共 {{ records.length }} 筆記錄</span>
          </div>
          <div class="divide-y divide-gray-100">
            <div v-for="record in records" :key="record.id" class="px-6 py-4 flex items-center justify-between hover:bg-gray-50 transition">
              <div class="flex items-center space-x-4">
                <div class="p-3 rounded-xl text-xl" :class="record.isGreen ? 'bg-emerald-50' : 'bg-gray-100'">
                  {{ record.icon }}
                </div>
                <div>
                  <h4 class="font-semibold text-gray-900">{{ record.title }}</h4>
                  <div class="flex space-x-2 items-center mt-0.5">
                    <span class="text-xs bg-gray-100 text-gray-600 px-2 py-0.5 rounded">{{ record.category }}</span>
                    <span v-if="record.isGreen" class="text-[10px] bg-emerald-100 text-emerald-700 font-bold px-1.5 py-0.5 rounded">低碳標章</span>
                  </div>
                </div>
              </div>
              <div class="text-right">
                <p class="font-bold text-gray-900">${{ record.amount }}</p>
                <p class="text-xs text-gray-400 font-mono">+{{ record.carbon }} kg CO₂</p>
              </div>
            </div>
            <div v-if="records.length === 0" class="p-8 text-center text-gray-400">
              目前尚無消費紀錄，請在右側新增！
            </div>
          </div>
        </div>

      </div>

      <!-- 右側：新增消費表單 -->
      <div class="bg-white p-6 rounded-2xl shadow-sm border border-gray-100 h-fit sticky top-8">
        <h3 class="text-lg font-bold text-gray-900 mb-6 flex items-center space-x-2">
          <span>📝</span>
          <span>新增一筆消費</span>
        </h3>
        
        <form @submit.prevent="addRecord" class="space-y-5">
          <!-- 消費名稱 -->
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-1">消費項目</label>
            <input v-model="form.title" type="text" placeholder="例如：捷運搭乘、排骨便當" required class="w-full px-4 py-2 border border-gray-200 rounded-xl focus:ring-2 focus:ring-emerald-500 focus:border-emerald-500 outline-none transition" />
          </div>

          <!-- 金額 -->
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-1">消費金額 (TWD)</label>
            <input v-model.number="form.amount" type="number" min="1" placeholder="請輸入金額" required class="w-full px-4 py-2 border border-gray-200 rounded-xl focus:ring-2 focus:ring-emerald-500 focus:border-emerald-500 outline-none transition" />
          </div>

          <!-- 分類選擇 -->
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-1">消費類別</label>
            <select v-model="form.category" class="w-full px-4 py-2 border border-gray-200 rounded-xl focus:ring-2 focus:ring-emerald-500 focus:border-emerald-500 outline-none transition bg-white">
              <option value="交通">🚗 交通運輸</option>
              <option value="飲食">🍔 餐飲飲食</option>
              <option value="購物">🛍️ 生活購物</option>
              <option value="娛樂">🎬 娛樂休閒</option>
            </select>
          </div>

          <!-- 環保選項（會影響碳足跡計算） -->
          <div class="bg-emerald-50/50 p-4 rounded-xl border border-emerald-100">
            <label class="flex items-center space-x-3 cursor-pointer">
              <input v-model="form.isGreen" type="checkbox" class="w-4 h-4 text-emerald-600 border-gray-300 rounded focus:ring-emerald-500" />
              <div>
                <span class="block text-sm font-semibold text-gray-800">這是一筆「綠色低碳」消費</span>
                <span class="block text-xs text-gray-500 mt-0.5">如：大眾運輸、蔬食、無包裝商品、電子發票</span>
              </div>
            </label>
          </div>

          <!-- 送出按鈕 -->
          <button type="submit" class="w-full bg-gradient-to-r from-emerald-600 to-teal-600 text-white font-bold py-3 rounded-xl shadow-md hover:opacity-90 active:scale-[0.98] transition">
            儲存紀錄
          </button>
        </form>
      </div>

    </main>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';

// 1. 初始化記帳範例資料
const records = ref([
  { id: 1, title: '搭乘台灣高鐵', amount: 1500, category: '交通', isGreen: true, carbon: 5.2, icon: '🚄' },
  { id: 2, title: '連鎖牛排店晚餐', amount: 450, category: '飲食', isGreen: false, carbon: 12.8, icon: '🥩' },
  { id: 3, title: '自備環保杯買植物奶拿鐵', amount: 85, category: '飲食', isGreen: true, carbon: 0.4, icon: '☕' },
  { id: 4, title: '汽車加油 (95無鉛)', amount: 1200, category: '交通', isGreen: false, carbon: 24.5, icon: '⛽' },
]);

// 2. 表單綁定資料
const form = ref({
  title: '',
  amount: null,
  category: '飲食',
  isGreen: false
});

// 3. 計算各項統計數據
const totalExpense = computed(() => {
  return records.value.reduce((sum, item) => sum + item.amount, 0);
});

const totalCarbon = computed(() => {
  return records.value.reduce((sum, item) => sum + item.carbon, 0);
});

const greenRatio = computed(() => {
  if (records.value.length === 0) return 0;
  const greenCount = records.value.filter(item => item.isGreen).length;
  return Math.round((greenCount / records.value.length) * 100);
});

// 4. 定義不同類別與條件下的碳足跡係數 (僅為模擬值)
const getCarbonEstimate = (category, amount, isGreen) => {
  let baseFactor = 0.01; // 每花 1 元產生的基本排碳係數
  if (category === '交通') baseFactor = isGreen ? 0.003 : 0.02;
  if (category === '飲食') baseFactor = isGreen ? 0.005 : 0.03;
  if (category === '購物') baseFactor = isGreen ? 0.002 : 0.015;
  if (category === '娛樂') baseFactor = 0.008;
  
  return parseFloat((amount * baseFactor).toFixed(1));
};

// 5. 根據類別給予圖示
const getIcon = (category, isGreen) => {
  if (category === '交通') return isGreen ? '🚲' : '🚗';
  if (category === '飲食') return isGreen ? '🥗' : '🍔';
  if (category === '購物') return '🛍️';
  return '🎬';
};

// 6. 新增記錄方法
const addRecord = () => {
