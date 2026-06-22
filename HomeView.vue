<script setup>
import { ref, computed } from 'vue'
import Sidebar from '@/components/Sidebar.vue'
import Login from '@/components/Login.vue' 
import { UserRound, Crown, Heart } from 'lucide-vue-next'
import { useRouter } from 'vue-router'
import { useBarberStore } from '@/composables/useBarberStore'
import { useFavoritesStore } from '@/composables/useFavoritesStore'

const router = useRouter()
const barberStore = useBarberStore()
const favoritesStore = useFavoritesStore()

// SIDEBAR TOGGLE: Controla el estado de la sidebar minimizada
const sidebarMinimized = ref(false)
const handleToggleSidebar = (isMinimized) => {
  sidebarMinimized.value = isMinimized
}

const showLogin = ref(false)
const searchQuery = ref('')

const heroImage = 'https://images.unsplash.com/photo-1517832606299-7ae9b720a186?q=80&w=1200&auto=format&fit=crop'

const filteredBarbers = computed(() => {
  return barberStore.barbers.value.filter(barber =>
    barber.nombre.toLowerCase().includes(searchQuery.value.toLowerCase())
  )
})

function toggleFavorite(barberId) {
  favoritesStore.toggleFavorite(barberId)
}

function isFavorite(barberId) {
  return favoritesStore.isFavorite(barberId)
}
</script>

<template>
  <div class="page-shell">
    <Sidebar @openLogin="showLogin = true" @toggleSidebar="handleToggleSidebar" />

    <main :class="['main-content', { 'sidebar-minimized': sidebarMinimized }]">
      <header class="topbar">
        <div>
          <h1 class="intro">Encuentra tu barbería ideal</h1>
          
          <p>Reserva cortes y servicios en toda la ciudad</p>
          <button class="create-btn" @click="router.push('/create')">AGREGA TU BARBERIA AQUÍ</button>
        </div>

        <div class="user-box">
          <input v-model="searchQuery" type="text" placeholder="Buscar barbería..." />
          <div class="avatar-box" @click="showLogin = true" role="button" aria-label="Abrir login">
            <UserRound class="user-icon" />
          </div>
        </div>
      </header>

      <section class="hero-banner">
        <div class="hero-info">
          <span class="badge">Top Barberías 2026</span>

          <h2>
            RESERVA TU PRÓXIMO
            <span>CORTE PREMIUM <Crown class="crown-icon" /></span>
          </h2>

          <p>
            Descubre barberías modernas con disponibilidad inmediata,
            estilos urbanos y atención profesional.
          </p>

          <button @click="router.push('/explore')">Reservar Ahora</button>
        </div>

        <div class="hero-image" :style="{ backgroundImage: `url(${heroImage})` }"></div>
      </section>

      <section class="categories">
        <button class="active">Todos</button>
        <button>Fade</button>
        <button>Barba</button>
        <button>Premium</button>
        <button>Express</button>
        <button>VIP</button>
      </section>

      <section class="cards-grid">
        <div v-if="filteredBarbers.length === 0" class="empty-state">
          <p>No hay barberías disponibles</p>
          <button class="create-btn" @click="router.push('/create')">Crea una barbería</button>
        </div>

        <div
          v-for="barber in filteredBarbers"
          :key="barber.id"
          class="card"
          @click="router.push(`/barbershop/${barber.id}`)"
          role="button"
        >
          <div class="card-image" :style="{ backgroundImage: `url(${barber.imagen})` }">
            <div class="rating">⭐ {{ barber.rating }}</div>
            <button
              class="favorite-btn"
              @click.stop="toggleFavorite(barber.id)"
              :class="{ 'is-favorite': isFavorite(barber.id) }"
            >
              <Heart :fill="isFavorite(barber.id) ? 'currentColor' : 'none'" />
            </button>
          </div>

          <div class="card-body">
            <div class="card-header">
              <div>
                <h3>{{ barber.nombre }}</h3>
                <p>{{ barber.direccion }} · {{ barber.ciudad }}</p>
              </div>
              <span class="price">${{ barber.precio }}</span>
            </div>

            <div class="tags">
              <span v-for="servicio in barber.servicios" :key="servicio">
                {{ servicio }}
              </span>
            </div>

            <div class="card-footer">
              <div class="status" :class="barber.disponible ? 'open' : 'closed'"></div>
              <small>{{ barber.disponible ? 'Disponible ahora' : 'Cerrado' }}</small>
              <button @click.stop="router.push(`/barbershop/${barber.id}`)">Agendar</button>
            </div>
          </div>
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

@font-face {
    font-family: 'FuenteBarber';
    src: url('../fonts/BarberStreet.ttf') format('opentype'); 
    font-weight: normal;
    font-style: normal;
}

*{
  margin:0;
  padding:0;
  box-sizing:border-box;
}

html, body {
  min-height:100%;
}

body{
  font-family:'Inter',sans-serif;
  background:linear-gradient(45deg, #111111, #000000);
  color:white;
  display:flex;
  min-height:100vh;
}

.page-shell {
  display:flex;
  width:100%;
}

.main-content{
  margin-left:280px;
  width:calc(100% - 280px);
  padding:35px 100px 0;
  transition: margin-left 0.3s ease, width 0.3s ease, padding 0.3s ease;
}

.main-content.sidebar-minimized {
  margin-left: 100px;
  width: calc(100% - 80px);
  padding: 35px 120px 0;
}

.topbar{
  display:flex;
  justify-content:space-between;
  align-items:center;
  margin-bottom:35px;
}

.topbar h1{
  font-size:2.2rem;
  margin-bottom:8px;
}

.topbar p{
  color:#9ca3af;
}

.intro{
  font-family: 'FuenteBlesh', sans-serif;
}

.user-box{
  display:flex;
  align-items:center;
  gap:20px;
}

.create-btn {
  border: none;
  background: linear-gradient(135deg, #BF924B, #ffb743);
  color: #000000;
  padding: 14px 24px;
  border-radius: 14px;
  font-weight: 700;
  cursor: pointer;
  font-size: 0.95rem;
  transition: 0.3s ease;
  margin-top: 15px;
}

.create-btn:hover {
  transform: translateY(-2px);
  box-shadow: 0 8px 24px rgba(191, 146, 75, 0.3);
}

.user-box input{
  background:#1f2937;
  border:none;
  padding:15px 20px;
  border-radius:14px;
  color:white;
  width:280px;
  outline:none;
}

.avatar-box{
  width:52px;
  height:52px;
  display:flex;
  align-items:center;
  justify-content:center;
  border-radius:12px;
  background:linear-gradient(135deg, rgba(191,146,75,0.12), rgba(255,183,67,0.08));
  border:1px solid rgba(255,255,255,0.06);
  transition: transform 0.18s ease, box-shadow 0.18s ease;
  cursor: pointer;
}

.avatar-box:hover{
  transform: translateY(-2px);
  box-shadow: 0 8px 24px rgba(0,0,0,0.6);
}

.user-icon{
  color: #BF924B;
  width:28px;
  height:28px;
}

.hero-banner{
  background:#0e0e0e;
  border-radius:32px;
  border: 1px solid rgba(255, 255, 255, 0.479);
  padding:50px;
  display:flex;
  align-items:center;
  justify-content:space-between;
  overflow:hidden;
  margin-bottom:35px;
  position:relative;
}

.hero-banner::before{
  content:'';
  position:absolute;
  width:500px;
  height:500px;
  background:rgba(226, 226, 226, 0.12);
  border-radius:50%;
  top:-200px;
  right:-150px;
}

.hero-info{
  max-width:550px;
  z-index:2;
}

.badge{
  background:rgba(245,158,11,0.15);
  color:#BF924B;
  padding:10px 18px;
  border-radius:50px;
  display:inline-block;
  margin-bottom:25px;
  font-size:0.9rem;
}

.hero-info h2{
  font-family: 'FuenteBarber', sans-serif;
  font-weight: normal;
  font-size:3rem;
  letter-spacing: 4px;
  line-height:1.1;
  margin-bottom:20px;
}

.hero-info h2 span{
  color:#BF924B;
}

.hero-info p{
  color:#9ca3af;
  line-height:1.7;
  margin-bottom:35px;
}

.hero-info button{
  border:none;
  background:linear-gradient(135deg,#BF924B,#ffb743);
  color:black;
  padding:18px 28px;
  border-radius:16px;
  font-weight:700;
  cursor:pointer;
  font-size:1rem;
}

.hero-image{
  width:400px;
  height:450px;
  border-radius:28px;
  background-size:cover;
  background-position:center;
  z-index:2;
}

.categories{
  display:flex;
  gap:15px;
  margin-bottom:35px;
  flex-wrap:wrap;
}

.categories button{
  border:none;
  background:#1f2937;
  color:#9ca3af;
  padding:14px 22px;
  border-radius:14px;
  cursor:pointer;
  transition:0.3s;
}

.categories button:hover,
.categories .active{
  background:#BF924B;
  color:black;
}

.cards-grid{
  display:grid;
  grid-template-columns:repeat(auto-fit,minmax(320px,1fr));
  gap:30px;
}

.card{
  background:#272211;
  border-radius:28px;
  overflow:hidden;
  transition:0.4s;
  border:1px solid rgba(255,255,255,0.04);
}

.card:hover{
  transform:translateY(-10px);
  box-shadow:0 20px 40px rgba(0,0,0,0.35);
}

.card-image{
  height:250px;
  position:relative;
  background-size:cover;
  background-position:center;
}

.rating{
  position:absolute;
  top:18px;
  right:18px;
  background:white;
  color:black;
  padding:10px 14px;
  border-radius:12px;
  font-weight:600;
}

.favorite-btn {
  position: absolute;
  top: 18px;
  left: 18px;
  background: rgba(255, 255, 255, 0.9);
  border: none;
  border-radius: 50%;
  width: 44px;
  height: 44px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: 0.3s ease;
  color: #6b7280;
}

.favorite-btn:hover {
  background: white;
  transform: scale(1.1);
}

.favorite-btn.is-favorite {
  color: #ef4444;
}

.card-body{
  padding:25px;
}

.card-header{
  display:flex;
  justify-content:space-between;
  align-items:flex-start;
  margin-bottom:20px;
}

.card-header h3{
  font-size:1.3rem;
  margin-bottom:6px;
}

.card-header p{
  color:#9ca3af;
}

.price{
  color:#BF924B;
  font-weight:700;
  font-size:1.1rem;
}

.tags{
  display:flex;
  gap:10px;
  flex-wrap:wrap;
  margin-bottom:25px;
}

.tags span{
  background:#37331f;
  color:#d1d5db;
  padding:10px 14px;
  border-radius:50px;
  font-size:0.85rem;
}

.crown-icon {
  width: 40%;
  height: 50px;
  color: #3d2f18;
  fill: #a7782d;
  margin-left: -100px;
}

.card-footer{
  display:flex;
  align-items:center;
  gap:10px;
}

.status{
  width:12px;
  height:12px;
  border-radius:50%;
}

.open{
  background:#22c55e;
}

.closed{
  background:#ef4444;
}

.card-footer small{
  color:#9ca3af;
  flex:1;
}

.card-footer button{
  border:none;
  background:#BF924B;
  color:black;
  padding:12px 18px;
  border-radius:12px;
  font-weight:600;
  cursor:pointer;
}

@media(max-width:1200px){
  .hero-banner{
    flex-direction:column;
    gap:40px;
  }

  .hero-image{
    width:100%;
    max-width:500px;
  }
}

@media(max-width:900px){
  .sidebar{
    display:none;
  }

  .main-content{
    width:100%;
    margin-left:0;
  }

  .topbar{
    flex-direction:column;
    align-items:flex-start;
    gap:25px;
  }

  .user-box{
    width:100%;
  }

  .user-box input{
    width:100%;
  }

  .hero-info h2{
    font-size:2.8rem;
  }
}

@media(max-width:600px){
  .main-content{
    padding:20px;
  }

  .hero-banner{
    padding:30px;
  }

  .hero-info h2{
    font-size:2.2rem;
  }

  .hero-image{
    height:300px;
  }
}

.empty-state {
  grid-column: 1 / -1;
  text-align: center;
  padding: 60px 40px;
  background: rgba(255, 255, 255, 0.02);
  border-radius: 20px;
  border: 1px solid rgba(255, 255, 255, 0.05);
}

.empty-state p {
  color: #9ca3af;
  font-size: 1.1rem;
  margin-bottom: 20px;
}

.empty-state .create-btn {
  margin-top: 0;
}
</style>
