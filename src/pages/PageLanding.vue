<template>
  <q-layout view="hHh lpR fFf">
    <q-header elevated class="bg-primary text-white">
      <q-toolbar>
        <q-btn
          flat
          round
          dense
          icon="menu"
          class="lt-md"
          @click="leftDrawerOpen = !leftDrawerOpen"
        />
        <q-toolbar-title class="row items-center">
          <q-avatar square size="28px" class="q-mr-sm">
            <img src="https://cdn.quasar.dev/logo/svg/quasar-logo.svg" alt="logo" />
          </q-avatar>
          <span class="text-weight-bold">Equipe</span>
        </q-toolbar-title>

        <div class="gt-sm row items-center q-gutter-sm">
          <q-btn flat label="Equipe" @click="scrollTo('cards')" />
          <q-btn flat label="Depoimentos" @click="scrollTo('testimonials')" />
          <q-separator vertical inset class="q-mx-sm" />

          <q-toggle
            :model-value="$q.dark.isActive"
            color="white"
            checked-icon="dark_mode"
            unchecked-icon="light_mode"
            :label="$q.dark.isActive ? 'Dark' : 'Light'"
            class="q-ml-md"
            @update:model-value="(val) => $q.dark.set(val)"
          />
        </div>
      </q-toolbar>
    </q-header>

    <q-drawer
      v-model="leftDrawerOpen"
      side="left"
      overlay
      :class="$q.dark.isActive ? 'bg-dark text-white' : 'bg-grey-1'"
    >
      <q-list bordered padding>
        <q-item clickable v-ripple @click="navAndClose('cards')">
          <q-item-section>Equipe</q-item-section>
        </q-item>
        <q-item clickable v-ripple @click="navAndClose('testimonials')">
          <q-item-section>Depoimentos</q-item-section>
        </q-item>
        <q-separator spaced />
        <q-item>
          <q-item-section>
            <!-- toggle ligado ao Dark Plugin -->
            <q-toggle
              :model-value="$q.dark.isActive"
              color="primary"
              checked-icon="dark_mode"
              unchecked-icon="light_mode"
              :label="$q.dark.isActive ? 'Dark' : 'Light'"
              @update:model-value="(val) => $q.dark.set(val)"
            />
          </q-item-section>
        </q-item>
      </q-list>
    </q-drawer>

    <q-page-container>
      <q-page class="page">
        <!-- HERO -->
        <section class="hero q-py-xl text-white">
          <div class="container">
            <div class="row items-center">
              <div class="col-12 col-md-7 q-pr-xl">
                <div class="text-h3 text-weight-bold q-mb-md">
                  Conheça a <span class="brand">nossa equipe</span>
                </div>
                <div class="text-subtitle1 q-mb-lg opacity-80">
                  Uma LP de brincadeira pra mostrar Quasar na prática: cards dos membros da equipe e
                  depoimentos que só quem vive o dev-dia-a-dia entende 😄
                </div>
                <div class="row q-gutter-sm">
                  <q-btn
                    color="accent"
                    size="lg"
                    unelevated
                    label="Ver equipe"
                    @click="scrollTo('cards')"
                  />
                </div>
              </div>
            </div>
          </div>
        </section>

        <!-- CARDS -->
        <section :id="ids.cards" class="q-py-xl section">
          <div class="container">
            <div class="text-h4 text-center q-mb-md">Devs da Imendes</div>
            <div class="text-subtitle2 text-center q-mb-xl opacity-80">
              Clique em "ver mais" para a ficha completa com piadas internas.
            </div>

            <div class="row q-col-gutter-xl">
              <div v-for="(p, i) in people" :key="i" class="col-12 col-sm-6 col-md-4">
                <q-card bordered flat class="q-pa-sm hero-card-item">
                  <q-img
                    :src="p.photo || placeholderImg"
                    :ratio="4 / 3"
                    :alt="p.name"
                    spinner-color="primary"
                  />
                  <q-card-section>
                    <div class="row items-center justify-between">
                      <div>
                        <div class="text-subtitle1 text-weight-medium">{{ p.name }}</div>
                        <div class="text-caption text-grey-7">Habilidade: {{ p.power }}</div>
                      </div>
                      <q-icon :name="p.icon" size="md" class="text-primary" />
                    </div>
                    <div class="q-mt-sm">
                      <q-badge
                        v-for="(tag, j) in p.tags"
                        :key="j"
                        color="accent"
                        outline
                        class="q-mr-sm q-mb-sm"
                        >{{ tag }}</q-badge
                      >
                    </div>
                  </q-card-section>
                  <q-separator />
                  <q-card-actions align="between">
                    <q-btn flat label="Ver mais" @click="openProfile(p)" />
                    <q-btn flat icon="favorite" :label="p.kudos + ' kudos'" @click="p.kudos++" />
                  </q-card-actions>
                </q-card>
              </div>
            </div>
          </div>
        </section>

        <!-- MODAL PERFIL -->
        <q-dialog v-model="profileDialog">
          <q-card style="max-width: 700px; width: 95vw">
            <q-card-section class="row items-center q-gutter-md">
              <q-avatar size="96px">
                <img :src="current?.photo || placeholderImg" :alt="current?.name" />
              </q-avatar>
              <div>
                <div class="text-h6">{{ current?.name }}</div>
                <div class="text-caption">Habilidade: {{ current?.power }}</div>
                <div class="text-caption">Nível de café: {{ current?.stats.coffee }}/10 ☕</div>
                <div class="text-caption">Commits por dia: ~{{ current?.stats.commits }}</div>
              </div>
            </q-card-section>
            <q-separator />
            <q-card-section>
              <div class="text-subtitle2 q-mb-sm">Piadas internas</div>
              <ul class="q-pl-md">
                <li v-for="(j, i) in current?.jokes" :key="i" class="text-body2 opacity-80">
                  {{ j }}
                </li>
              </ul>
            </q-card-section>
            <q-card-actions align="right">
              <q-btn flat label="Fechar" v-close-popup />
            </q-card-actions>
          </q-card>
        </q-dialog>

        <!-- DEPOIMENTOS FALSOS -->
        <section :id="ids.testimonials" class="q-py-xl section">
          <div class="container">
            <div class="text-h4 text-center q-mb-xl">Depoimentos (muito) sérios</div>
            <div class="row q-col-gutter-xl">
              <div v-for="(t, i) in testimonials" :key="i" class="col-12 col-md-4">
                <q-card bordered flat class="q-pa-md">
                  <q-card-section class="text-body2">“{{ t.quote }}”</q-card-section>
                  <q-separator inset />
                  <q-card-section class="row items-center q-gutter-sm">
                    <q-avatar><img :src="t.avatar" :alt="t.name" /></q-avatar>
                    <div>
                      <div class="text-subtitle2">{{ t.name }}</div>
                      <div class="text-caption text-grey">{{ t.role }}</div>
                    </div>
                  </q-card-section>
                </q-card>
              </div>
            </div>
          </div>
        </section>

        <!-- FOOTER -->
        <q-footer class="bg-grey-9 text-white q-py-md">
          <div class="container row items-center justify-between">
            <div class="text-caption">
              © {{ new Date().getFullYear() }} Brincadeiras da Equipe.
            </div>
            <div class="row q-gutter-sm">
              <q-btn flat dense icon="mdi-twitter" href="#" target="_blank" />
              <q-btn flat dense icon="mdi-instagram" href="#" target="_blank" />
              <q-btn flat dense icon="mdi-youtube" href="#" target="_blank" />
            </div>
          </div>
        </q-footer>
      </q-page>
    </q-page-container>
  </q-layout>
</template>

<script setup>
import { ref } from 'vue'
import { useQuasar } from 'quasar'

const $q = useQuasar()
const leftDrawerOpen = ref(false)
const ids = { cards: 'cards', testimonials: 'testimonials' }

function scrollTo(elId) {
  const el = document.getElementById(elId)
  if (el) el.scrollIntoView({ behavior: 'smooth', block: 'start' })
}
function navAndClose(id) {
  scrollTo(id)
  leftDrawerOpen.value = false
}

const placeholderImg = 'https://i.pravatar.cc/600?img=5'
const people = ref([
  {
    name: 'Leonardo (China)',
    power: '"Isso aí é simples"',
    icon: 'sailing',
    photo: new URL('../assets/equipe/China.png', import.meta.url).href,
    tags: ['o eterno estagiário', 'pirata dev'],
    jokes: [
      'Diagnóstico padrão: "isso aí é simples".',
      'Viu no StackOverflow que era só reiniciar o banco… e deu ruim.',
    ],
    kudos: 7,
    stats: { coffee: 6, commits: 21 },
  },
  {
    name: 'Elias (Cleitin)',
    power: 'Dados, BIs e… quebra-código',
    icon: 'table_view',
    photo: new URL('../assets/equipe/cleitin.png', import.meta.url).href,
    tags: ['Power BI', 'data vibes', 'caso à parte'],
    jokes: [
      'Uma vez apagou a TABELA DE CLIENTES em produção achando que era QA. 🫠',
      'Tem certificação em Ctrl+Z emocional.',
      'Imagem clássica: “quebrar códigos”.',
    ],
    kudos: 9,
    stats: { coffee: 9, commits: 33 },
  },
  {
    name: 'Israel',
    power: 'Engenheiro de prompt',
    icon: 'psychology',
    photo: new URL('../assets/equipe/Israel.png', import.meta.url).href,
    tags: ['veio do fiscal', 'estagiário'],
    jokes: [
      'Quando o gestor vira as costas, dá uma tremidinha e bate a cabeça na mesa (de leve).',
      'Conversa com a IA como se fosse gente.',
      'Trouxe compliance fiscal… pro prompt.',
    ],
    kudos: 4,
    stats: { coffee: 5, commits: 19 },
  },
  {
    name: 'Renzo',
    power: 'Sênior, piadista e +100 de café',
    icon: 'coffee',
    photo: new URL('../assets/equipe/Renzo.png', import.meta.url).href,
    tags: ['senior', 'cowboy do deploy'],
    jokes: [
      'Faz piada até com erro 500.',
      'Se o CI travar, ele oferece café e funciona.',
      'Já chegou com chapéu de cowboy nas reuniões.',
    ],
    kudos: 6,
    stats: { coffee: 10, commits: 40 },
  },
  {
    name: 'Diego',
    power: 'Belezinha-driven development',
    icon: 'spa',
    photo: new URL('../assets/equipe/Diego.png', import.meta.url).href,
    tags: ['chá lover', 'químico', 'ewok'],
    jokes: [
      'Bordão oficial: “belezinha”.',
      'Mestrado em química — coincidências com Walter White puramente acidentais.',
      'Já se protegeu do ar-condicionado com a blusa na cabeça e virou um Ewok.',
    ],
    kudos: 5,
    stats: { coffee: 3, commits: 24 },
  },
  {
    name: 'Jullia (vulgo eu)',
    power: 'Front imbatível',
    icon: 'code',
    photo: new URL('../assets/equipe/eu2.jpeg', import.meta.url).href,
    tags: ['frontend', 'não é culpa do front', 'lead da LP'],
    jokes: [
      '“A culpa nunca é do front e sim do backend.”',
      'Responsável por essa LP de zoeira. 😎',
    ],
    kudos: 12,
    stats: { coffee: 7, commits: 37 },
  },
])

const profileDialog = ref(false)
const current = ref(null)
function openProfile(p) {
  current.value = p
  profileDialog.value = true
}

const testimonials = [
  {
    quote: 'O China disse que era simples. Era um cluster. Mas rimos (depois).',
    name: 'Alguém do time',
    role: 'Sobrevivente do deploy',
    avatar: 'https://i.pravatar.cc/80?img=10',
  },
  {
    quote: 'Cleitin quase mandou a base embora, mas os gráficos do Power BI ficaram lindos.',
    name: 'DBA Anônimo',
    role: 'Terapeuta do banco',
    avatar: 'https://i.pravatar.cc/80?img=20',
  },
  {
    quote: 'Israel conversa com a IA e, às vezes, com a mesa. Resultados impressionantes.',
    name: 'Agile Coach',
    role: 'Observador de rituais',
    avatar: 'https://i.pravatar.cc/80?img=30',
  },
  {
    quote: 'Renzo mede performance em xícaras de café por commit.',
    name: 'CI/CD',
    role: 'Runner #1',
    avatar: 'https://i.pravatar.cc/80?img=25',
  },
  {
    quote: '“Belezinha” é o novo LGTM. Aprovado.',
    name: 'Code Reviewer',
    role: 'Guardião do PR',
    avatar: 'https://i.pravatar.cc/80?img=15',
  },
  {
    quote: 'A Jullia provou: se quebrou, é o backend.',
    name: 'Usuário final',
    role: 'QA espontâneo',
    avatar: 'https://i.pravatar.cc/80?img=35',
  },
]
</script>

<style>
/* ===== Variáveis de tema ===== */
:root {
  --bg: #ffffff; /* fundo claro padrão */
  --section-bg: #ffffff;
  --alt-bg: #f7f7f7;
}

body.body--dark {
  --bg: #0d111a; /* fundo global escuro */
  --section-bg: #0d111a;
  --alt-bg: #141a24;
}

html,
body,
#q-app {
  min-height: 100%;
}

body,
.q-layout,
.q-page-container,
.q-page {
  background: var(--bg) !important;
}

.page {
  background: var(--bg);
}

.section {
  background: var(--section-bg);
}
.alt {
  background: var(--alt-bg);
}

.container {
  max-width: 1140px;
  margin: 0 auto;
  padding: 0 16px;
}

.hero {
  background: linear-gradient(135deg, #2e354c 0%, #3a4360 100%);
  color: #fff;
}

.brand {
  text-decoration: underline;
}
.opacity-80 {
  opacity: 0.8;
}

.hero-card {
  backdrop-filter: blur(6px);
  border-radius: 16px;
}

.hero-card-item {
  border-radius: 14px;
  overflow: hidden;
}

/* ===== Badges / icons ===== */
.q-badge.bg-accent {
  color: #2e354c !important;
  border-color: rgba(46, 53, 76, 0.2);
}

body.body--dark .q-badge.bg-accent {
  color: #0d111a !important;
  border-color: rgba(255, 255, 255, 0.12);
}

.q-card .q-icon {
  opacity: 0.9;
}
</style>
