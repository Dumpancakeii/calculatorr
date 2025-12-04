<template>
  <div class="subnet-calculator">
    <div class="calculator-header">
      <h2>Калькулятор подсетей</h2>
      <p>Введите IP-адрес и выберите маску сети для расчета</p>
    </div>

    <form @submit.prevent="calculate">
      <div class="form-group">
        <label for="ip-address">IP-адрес</label>
        <input
          id="ip-address"
          v-model="ipAddress"
          type="text"
          placeholder="192.168.1.150"
          :class="['form-control', { error: ipError }]"
          @keyup.enter="calculate"
          @input="clearError"
        />
        <span v-if="ipError" class="error-message">{{ ipError }}</span>
      </div>

      <div class="form-group">
        <label for="subnet-mask">Маска подсети</label>
        <select
          id="subnet-mask"
          v-model="selectedMask"
          class="form-control"
        >
          <option
            v-for="mask in SUBNET_MASKS"
            :key="mask.value"
            :value="mask.value"
          >
            {{ mask.label }}
          </option>
        </select>
      </div>

      <button
        type="submit"
        class="calculate-button"
        :disabled="!isFormValid"
      >
        Рассчитать
      </button>
    </form>

    <div v-if="showResults" class="results-section">
      <h3>Результаты расчета</h3>
      <div class="result-grid">
        <div class="result-item">
          <span class="result-label">Введенный IP</span>
          <span class="result-value">{{ ipAddress }}</span>
        </div>
        
        <div class="result-item">
          <span class="result-label">Выбранная маска</span>
          <span class="result-value">{{ selectedMaskLabel }}</span>
        </div>
        
        <div class="result-item">
          <span class="result-label">Адрес сети</span>
          <span class="result-value highlight">{{ networkAddress }}</span>
        </div>
        
        <div class="result-item">
          <span class="result-label">Количество адресов</span>
          <span class="result-value highlight">{{ addressesCount }}</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

// Константы для масок (временно здесь, потом вынести в отдельный файл)
const SUBNET_MASKS = [
  { value: '255.255.255.0', label: '24/255.255.255.0' },
  { value: '255.255.255.128', label: '25/255.255.255.128' },
  { value: '255.255.255.192', label: '26/255.255.255.192' },
  { value: '255.255.255.224', label: '27/255.255.255.224' },
  { value: '255.255.255.240', label: '28/255.255.255.240' },
  { value: '255.255.255.248', label: '29/255.255.255.248' },
  { value: '255.255.255.252', label: '30/255.255.255.252' },
  { value: '255.255.0.0', label: '16/255.255.0.0' },
  { value: '255.255.255.255', label: '32/255.255.255.255' }
]

// Реактивные переменные
const ipAddress = ref('192.168.1.150')
const selectedMask = ref('255.255.255.0')
const showResults = ref(false)
const ipError = ref('')

// Функция валидации IP
const isIpValid = (ip) => {
  return (
    /^(\d{1,3})\.(\d{1,3})\.(\d{1,3})\.(\d{1,3})$/.test(ip) &&
    ip.split('.').every((octet) => {
      const num = Number(octet)
      return num >= 0 && num <= 255 && octet === num.toString()
    })
  )
}

// Функция расчета адреса сети
const getNetworkAddress = (ip, mask) => {
  const ipOctets = ip.split('.')
  const maskOctets = mask.split('.')
  const result = []

  for (let i = 0; i < 4; i++) {
    result.push(Number(ipOctets[i]) & Number(maskOctets[i]))
  }

  return `${result[0]}.${result[1]}.${result[2]}.${result[3]}`
}

// Функция расчета количества адресов
const getAddressesCount = (mask) => {
  let binaryMask = ''
  
  for (const octet of mask.split('.')) {
    binaryMask += Number(octet).toString(2).padStart(8, '0')
  }
  
  const zeros = 32 - binaryMask.replaceAll('0', '').length
  
  if (zeros === 0) return 1
  if (zeros === 1) return 2
  
  return Math.pow(2, zeros) - 2
}

// Вычисляемые свойства
const selectedMaskLabel = computed(() => {
  const mask = SUBNET_MASKS.find(m => m.value === selectedMask.value)
  return mask?.label || selectedMask.value
})

const isFormValid = computed(() => {
  return isIpValid(ipAddress.value)
})

const networkAddress = computed(() => {
  return getNetworkAddress(ipAddress.value, selectedMask.value)
})

const addressesCount = computed(() => {
  return getAddressesCount(selectedMask.value)
})

// Методы
const calculate = () => {
  if (!isFormValid.value) {
    ipError.value = 'Введите корректный IP-адрес'
    return
  }
  
  showResults.value = true
}

const clearError = () => {
  ipError.value = ''
  if (!isFormValid.value) {
    showResults.value = false
  }
}
</script>

<style scoped>
.subnet-calculator {
  --primary-color: #2563eb;
  --primary-hover: #1d4ed8;
  --success-color: #10b981;
  --error-color: #ef4444;
  --border-color: #e5e7eb;
  --bg-color: #ffffff;
  --text-primary: #111827;
  --text-secondary: #6b7280;
  --shadow: 0 4px 6px -1px rgb(0 0 0 / 0.1);
  --radius: 8px;
  
  max-width: 500px;
  margin: 0 auto;
  padding: 2rem;
  background: var(--bg-color);
  border-radius: var(--radius);
  box-shadow: var(--shadow);
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
}

.calculator-header {
  margin-bottom: 2rem;
  text-align: center;
}

.calculator-header h2 {
  color: var(--text-primary);
  font-size: 1.5rem;
  font-weight: 600;
  margin-bottom: 0.5rem;
}

.calculator-header p {
  color: var(--text-secondary);
  font-size: 0.875rem;
}

.form-group {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
  margin-bottom: 1.5rem;
}

.form-group label {
  color: var(--text-primary);
  font-weight: 500;
  font-size: 0.875rem;
}

.form-control {
  padding: 0.625rem 0.875rem;
  border: 1px solid var(--border-color);
  border-radius: var(--radius);
  font-size: 0.875rem;
  transition: border-color 0.15s ease;
}

.form-control:focus {
  outline: none;
  border-color: var(--primary-color);
  box-shadow: 0 0 0 3px rgba(37, 99, 235, 0.1);
}

.form-control.error {
  border-color: var(--error-color);
}

.error-message {
  color: var(--error-color);
  font-size: 0.75rem;
  margin-top: 0.25rem;
}

.calculate-button {
  width: 100%;
  padding: 0.75rem;
  background: var(--primary-color);
  color: white;
  border: none;
  border-radius: var(--radius);
  font-weight: 500;
  font-size: 0.875rem;
  cursor: pointer;
  transition: background-color 0.15s ease;
}

.calculate-button:hover:not(:disabled) {
  background: var(--primary-hover);
}

.calculate-button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.results-section {
  margin-top: 2rem;
  padding-top: 1.5rem;
  border-top: 1px solid var(--border-color);
}

.results-section h3 {
  color: var(--text-primary);
  font-size: 1.125rem;
  font-weight: 600;
  margin-bottom: 1rem;
}

.result-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 1rem;
}

.result-item {
  background: #f9fafb;
  padding: 1rem;
  border-radius: var(--radius);
  border: 1px solid var(--border-color);
}

.result-label {
  display: block;
  color: var(--text-secondary);
  font-size: 0.75rem;
  font-weight: 500;
  text-transform: uppercase;
  letter-spacing: 0.05em;
  margin-bottom: 0.25rem;
}

.result-value {
  display: block;
  color: var(--text-primary);
  font-size: 0.875rem;
  font-weight: 500;
  font-family: 'Monaco', 'Consolas', monospace;
}

.result-value.highlight {
  color: var(--success-color);
  font-weight: 600;
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.results-section {
  animation: fadeIn 0.3s ease-out;
}
</style>