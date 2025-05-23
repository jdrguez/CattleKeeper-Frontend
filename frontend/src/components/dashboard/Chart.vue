<template>
  <div>
    <div ref="chartContainer" style="width: 100%; height: 400px;"></div>
  </div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue'
import { useRouter } from 'vue-router'
import * as echarts from 'echarts'
import api from '@/api/axios'  

const chartContainer = ref(null)
let chartInstance = null
const router = useRouter()

const drawChart = (months, income, expense, net) => {
  if (!chartContainer.value) return

  chartInstance = echarts.init(chartContainer.value)

  const options = {
    title: {
      text: 'Monthly Income vs. Expenses',
      left: 'center'
    },
    tooltip: {
      trigger: 'axis'
    },
    legend: {
      data: ['Incomes', 'Expenses', 'Net'],
      top: 30
    },
    xAxis: {
      type: 'category',
      data: months
    },
    yAxis: {
      type: 'value'
    },
    series: [
      {
        name: 'Incomes',
        type: 'bar',
        data: income,
        itemStyle: { color: '#4caf50' }
      },
      {
        name: 'Expenses',
        type: 'bar',
        data: expense,
        itemStyle: { color: '#f44336' }
      },
      {
        name: 'Net',
        type: 'line',
        data: net,
        itemStyle: { color: '#2196f3' }
      }
    ]
  }

  chartInstance.setOption(options)
}

const loadData = async () => {
  try {
    const res = await api.get('/api/stats/monthly-summary/')
    const summary = res.data?.monthly_summary ?? []

    if (summary.length === 0) {
      console.warn('There is no data to display.')
      return
    }

    const months = summary.map(s => `${s.month}`.padStart(2, '0'))
    const income = summary.map(s => s.income)
    const expense = summary.map(s => s.expense)
    const net = summary.map(s => s.net)

    drawChart(months, income, expense, net)
  } catch (error) {
    if (error.response && error.response.status== 402){
      router.push('/plans')
    }else{
      console.error('Error getting data from backend:', error)
    }
  }
}

onMounted(loadData)
onBeforeUnmount(() => {
  if (chartInstance) {
    chartInstance.dispose()
  }
})
</script>

<style scoped>
</style>
