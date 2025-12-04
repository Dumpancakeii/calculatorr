<template>
  <div class="home-page">
    <div class="page-header">
      <h1>Калькулятор подсетей IPv4</h1>
      <p>Инструмент для расчета параметров подсетей</p>
    </div>
    
    <div class="page-content">
      <SubnetCalculator />
      
      <div class="info-section">
        <h2>Что такое подсеть?</h2>
        <p>
          Подсеть — это логическое разделение IP-сети на несколько 
          меньших сетевых сегментов. Используется для оптимизации 
          трафика, повышения безопасности и организации сети.
        </p>
        
        <h3>Основные понятия:</h3>
        <ul>
          <li><strong>IP-адрес</strong> — уникальный идентификатор устройства в сети</li>
          <li><strong>Маска подсети</strong> — определяет, какая часть адреса относится к сети, а какая — к хосту</li>
          <li><strong>Адрес сети</strong> — первый адрес в подсети, идентифицирует саму подсеть</li>
          <li><strong>Широковещательный адрес</strong> — последний адрес в подсети, используется для отправки данных всем устройствам в подсети</li>
          <li><strong>Доступные адреса</strong> — диапазон IP-адресов, которые могут быть назначены устройствам</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup>
import SubnetCalculator from '"C:\Users\vikto\OneDrive\Рабочий стол\"калькулятор подсетей\components.vue'
</script>

<style scoped>
.home-page {
  max-width: 1200px;
  margin: 0 auto;
  padding: 2rem 1rem;
}

.page-header {
  text-align: center;
  margin-bottom: 3rem;
  padding-bottom: 2rem;
  border-bottom: 1px solid #e5e7eb;
}

.page-header h1 {
  font-size: 2rem;
  font-weight: 700;
  color: #111827;
  margin-bottom: 0.5rem;
}

.page-header p {
  font-size: 1rem;
  color: #6b7280;
}

.page-content {
  display: flex;
  flex-direction: column;
  gap: 3rem;
}

.info-section {
  background: #f9fafb;
  padding: 2rem;
  border-radius: 12px;
  border: 1px solid #e5e7eb;
}

.info-section h2 {
  font-size: 1.25rem;
  font-weight: 600;
  color: #111827;
  margin-bottom: 1rem;
}

.info-section h3 {
  font-size: 1rem;
  font-weight: 600;
  color: #374151;
  margin: 1.5rem 0 1rem;
}

.info-section p {
  color: #4b5563;
  line-height: 1.6;
  margin-bottom: 1.5rem;
}

.info-section ul {
  list-style-type: disc;
  padding-left: 1.5rem;
  color: #4b5563;
  line-height: 1.8;
}

.info-section li {
  margin-bottom: 0.5rem;
}

.info-section strong {
  color: #111827;
  font-weight: 600;
}
</style>
