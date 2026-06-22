<script setup>
import { ref, computed } from 'vue'
import Sidebar from '@/components/Sidebar.vue';
import Login from '@/components/Login.vue'
import { useRouter } from 'vue-router'
import { Calendar, Scissors, CalendarDays, Calendars, BellPlus, ClockFading, Trash2 } from 'lucide-vue-next';
import { useAgendaStore } from '@/composables/useAgendaStore'

const router = useRouter()
const agendaStore = useAgendaStore()

// SIDEBAR TOGGLE: Controla el estado de la sidebar minimizada
const sidebarMinimized = ref(false)
const handleToggleSidebar = (isMinimized) => {
  sidebarMinimized.value = isMinimized
}

const showLogin = ref(false)

// Computed local para acceder a agendaBarbers de forma segura
const agendaBarbersList = computed(() => {
  const items = agendaStore.agendaBarbers
  return Array.isArray(items) ? items : []
})

function removeFromAgenda(barberId) {
  agendaStore.removeAgenda(barberId)
}

function viewBarbershop(barberId) {
  router.push(`/barbershop/${barberId}`)
}
</script>

<template>
  <div class="page-shell">
    <Sidebar @openLogin="showLogin = true" @toggleSidebar="handleToggleSidebar" />
    
    <main :class="['agenda-page', { 'sidebar-minimized': sidebarMinimized }]">

    <header class="agenda-header">
      <div>
        <h1>Tu Agenda</h1>
        <p>Gestiona y revisa tus reservas fácilmente.</p>
      </div>
      
      <button class="explore-btn" @click="router.push('/explore')">
        Explorar barberías
      </button>
    </header>

    <section class="agenda-banner">
      <div class="banner-content">
        <h2>Mantén tu estilo siempre fresco <Scissors class="scissorsicon" /> </h2>

        <p>
          Agenda cortes, guarda tus barberías favoritas y recibe
          recordatorios automáticos de tus próximas reservas.
        </p>
      </div>
    </section>

    <section v-if="agendaBarbersList.length === 0" class="empty-state">

      <div class="empty-icon">
        <CalendarDays class="calendaricon" />
      </div>

      <h2>No has agendado con ninguna barbería</h2>

      <p>
        Cuando reserves una cita, aparecerá aquí junto con la fecha,
        hora y detalles de la barbería.
      </p>

      <button class="reserve-btn" @click="router.push('/explore')">
        Reservar ahora
      </button>

    </section>

    <section v-else class="agenda-grid">
      <div
        v-for="item in agendaBarbersList"
        :key="item.id"
        class="agenda-card"
        @click="viewBarbershop(item.barberia.id)"
        role="button"
      >
        <div class="card-header">
          <h3>{{ item.barberia.nombre }}</h3>
          <button
            class="remove-btn"
            @click.stop="removeFromAgenda(item.barberia.id)"
            title="Cancelar cita"
          >
            <Trash2 class="trash-icon" />
          </button>
        </div>

        <div class="card-location">
          📍 {{ item.barberia.direccion }}, {{ item.barberia.ciudad }}
        </div>

        <div class="card-datetime">
          <div v-if="item.fecha" class="datetime-item">
            📅 Fecha: {{ item.fecha }}
          </div>
          <div v-if="item.hora" class="datetime-item">
            🕐 Hora: {{ item.hora }}
          </div>
        </div>

        <div class="card-footer">
          <span class="price">${{ item.barberia.precio }}</span>
          <button class="view-btn" @click.stop="viewBarbershop(item.barberia.id)">
            Ver detalles
          </button>
        </div>
      </div>
    </section>

    <section class="info-cards">

      <div class="info-card">
        <h3>Reservas rápidas <Calendars class="calendarsicon" /></h3>

        <p>
          Agenda citas en segundos desde cualquier barbería disponible.
        </p>
      </div>

      <div class="info-card">
        <h3>Recordatorios <BellPlus class="bellplusicon" /></h3>

        <p>
          Recibe notificaciones antes de cada corte para no olvidarte.
        </p>
      </div>

      <div class="info-card">
        <h3>Historial <ClockFading class="clockfadingicon" /></h3>

        <p>
          Consulta todas tus citas y servicios anteriores fácilmente.
        </p>
      </div>

    </section>

  </main>
    <Login v-if="showLogin" @close="showLogin = false" />
  </div>
</template>

<style>

@font-face {
    font-family: 'FuenteBlesh';
    src: url('../fonts/BleshForte.otf') format('opentype'); 
    font-weight: normal;
    font-style: normal;
}

@media (min-width: 1024px) {
  .about {
    min-height: 100vh;
    display: flex;
    align-items: center;
  }
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  background: #0d0d0d;
  color: white;
  font-family: Arial, sans-serif;
}

.agenda-page {
  margin-left: 280px;
  width: calc(100% - 280px);
  min-height: 100vh;
  padding: 40px;
  transition: margin-left 0.3s ease, width 0.3s ease;
}

.agenda-page.sidebar-minimized {
  margin-left: 80px;
  width: calc(100% - 80px);
  padding: 40px 60px;
}

.agenda-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 35px;
}

.agenda-header h1 {
  font-size: 2.4rem;
  margin-bottom: 8px;
  font-family: 'FuenteBlesh', sans-serif;
  background: linear-gradient(to right, #ffffff, #424242);
  background-clip: text;

  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}

.agenda-header p {
  color: #9ca3af;
  font-size: 1rem;
}

.explore-btn {
  background: linear-gradient(135deg, #c7984f, #f0c46d);

  border: none;
  padding: 14px 22px;
  border-radius: 14px;

  font-weight: bold;
  cursor: pointer;

  transition: 0.3s;
}

.explore-btn:hover {
  transform: translateY(-2px);
}

.agenda-banner {
  background:
    linear-gradient(
      135deg,
      rgba(196, 196, 196, 0.18),
      rgba(0, 0, 0, 0.7)
    );

  border: 1px solid rgba(255,255,255,0.06);

  padding: 35px;
  border-radius: 28px;

  margin-bottom: 40px;
}

.scissorsicon {
  margin-left: 2px;
  color: #f0c46d;
}

.banner-content h2 {
  font-size: 1.8rem;
  margin-bottom: 12px;
}

.banner-content p {
  color: #c9c9c9;
  max-width: 700px;
  line-height: 1.7;
}

.empty-state {
  background: #141414;

  border: 1px solid rgba(255,255,255,0.05);

  border-radius: 30px;

  padding: 60px 30px;

  text-align: center;

  margin-bottom: 40px;
}

.empty-icon {
  margin-bottom: 20px;
  margin-top: -10px;
  margin-left: auto;
  margin-right: auto;
}

.calendaricon {
  width: 99px;
  height: 99px;
  color: #f0c46d;
  margin-bottom: 20px;
}

.empty-state h2 {
  font-size: 1.9rem;
  margin-bottom: 15px;
}

.empty-state p {
  color: #9ca3af;

  max-width: 600px;

  margin: auto;

  line-height: 1.7;

  margin-bottom: 30px;
}

.reserve-btn {
  background: linear-gradient(135deg, #bf924b, #ffc465);

  border: none;

  color: black;

  padding: 15px 28px;

  border-radius: 14px;

  font-weight: bold;

  cursor: pointer;

  transition: 0.3s;
}

.reserve-btn:hover {
  transform: scale(1.03);
}

.info-cards {
  display: grid;

  grid-template-columns: repeat(3, 1fr);

  gap: 25px;
}

.info-card {
  background: #141414;

  border-radius: 24px;

  padding: 25px;

  border: 1px solid rgba(255,255,255,0.05);

  transition: 0.3s;
}

.info-card:hover {
  transform: translateY(-4px);
}

.info-card h3 {
  margin-bottom: 12px;
  color: #f0c46d;
}

.info-card p {
  color: #b3b3b3;
  line-height: 1.6;
}

.calendarsicon {
  width: 22px;
  height: 22px;
  color: #f0c46d;
  margin-left: 6px;
}

.bellplusicon {
  width: 22px;
  height: 22px;
  color: #f0c46d;
  margin-left: 6px;
}

.clockfadingicon {
  width: 22px;
  height: 22px;
  color: #f0c46d;
  margin-left: 6px;
}

.agenda-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
  gap: 25px;
  margin-bottom: 40px;
}

.agenda-card {
  background: linear-gradient(135deg, rgba(20, 20, 20, 0.8), rgba(30, 30, 30, 0.8));
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 16px;
  padding: 20px;
  cursor: pointer;
  transition: all 0.3s ease;
}

.agenda-card:hover {
  border-color: rgba(240, 196, 109, 0.5);
  transform: translateY(-4px);
  box-shadow: 0 10px 30px rgba(240, 196, 109, 0.1);
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 15px;
}

.card-header h3 {
  font-size: 1.3rem;
  margin: 0;
  color: #fff;
}

.remove-btn {
  background: rgba(255, 72, 72, 0.1);
  border: 1px solid rgba(255, 72, 72, 0.3);
  color: #ff4848;
  width: 36px;
  height: 36px;
  border-radius: 8px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: all 0.3s ease;
}

.remove-btn:hover {
  background: rgba(255, 72, 72, 0.2);
}

.trash-icon {
  width: 18px;
  height: 18px;
}

.card-location {
  color: #c9c9c9;
  font-size: 0.95rem;
  margin-bottom: 12px;
}

.card-datetime {
  background: rgba(240, 196, 109, 0.05);
  border-left: 3px solid #f0c46d;
  padding: 12px 15px;
  border-radius: 8px;
  margin-bottom: 15px;
}

.datetime-item {
  color: #f0c46d;
  font-size: 0.9rem;
  margin: 6px 0;
}

.card-footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding-top: 12px;
  border-top: 1px solid rgba(255, 255, 255, 0.05);
}

.price {
  font-size: 1.2rem;
  font-weight: 700;
  color: #f0c46d;
}

.view-btn {
  background: linear-gradient(135deg, #f0c46d, #ffc465);
  border: none;
  color: black;
  padding: 8px 16px;
  border-radius: 6px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  font-size: 0.9rem;
}

.view-btn:hover {
  transform: translateY(-2px);
}

@media (max-width: 1100px) {

  .info-cards {
    grid-template-columns: 1fr;
  }

  .agenda-header {
    flex-direction: column;
    align-items: flex-start;
    gap: 20px;
  }

  .agenda-page {
    margin-left: 0;
    padding: 25px;
  }
}

</style>