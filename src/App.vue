<script setup lang="ts">
import { onMounted, onBeforeUnmount } from 'vue'

let revealObserver: IntersectionObserver | null = null
let activeObserver: IntersectionObserver | null = null
let sections: HTMLElement[] = []
const videoUrl = '/product.mp4'
const museumPhotoUrl = '/museum.jpg'
let rafId = 0

function updateScrollMotion() {
  const viewportCenter = window.innerHeight / 2
  sections.forEach((section) => {
    const rect = section.getBoundingClientRect()
    const sectionCenter = rect.top + rect.height / 2
    const distance = Math.abs(sectionCenter - viewportCenter)
    const normalized = Math.min(1, distance / (window.innerHeight * 0.75))
    const focus = 1 - normalized

    section.style.setProperty('--focus', focus.toFixed(3))
    section.style.setProperty('--tilt', `${(0.5 - focus) * 2.2}deg`)
    section.style.setProperty('--lift', `${(1 - focus) * 26}px`)
  })
}

function onScroll() {
  cancelAnimationFrame(rafId)
  rafId = requestAnimationFrame(updateScrollMotion)
}

onMounted(() => {
  const items = document.querySelectorAll<HTMLElement>('.reveal')
  sections = Array.from(document.querySelectorAll<HTMLElement>('.section'))

  revealObserver = new IntersectionObserver(
    (entries) => {
      entries.forEach((entry) => {
        if (entry.isIntersecting) {
          const node = entry.target as HTMLElement
          node.classList.add('is-visible')
          revealObserver?.unobserve(node)
        }
      })
    },
    { threshold: 0.2, rootMargin: '0px 0px -10% 0px' },
  )

  items.forEach((item, index) => {
    item.style.setProperty('--delay', `${Math.min(index * 60, 360)}ms`)
    revealObserver?.observe(item)
  })

  activeObserver = new IntersectionObserver(
    (entries) => {
      entries.forEach((entry) => {
        ;(entry.target as HTMLElement).classList.toggle('is-active', entry.isIntersecting)
      })
    },
    { threshold: 0.62 },
  )

  sections.forEach((section) => activeObserver?.observe(section))

  updateScrollMotion()
  window.addEventListener('scroll', onScroll, { passive: true })
  window.addEventListener('resize', onScroll)
})

onBeforeUnmount(() => {
  revealObserver?.disconnect()
  activeObserver?.disconnect()
  window.removeEventListener('scroll', onScroll)
  window.removeEventListener('resize', onScroll)
  cancelAnimationFrame(rafId)
})
</script>

<template>
  <div class="page">
    <div class="ambient" aria-hidden="true"></div>

    <header class="hero section reveal">
      <h1 style="font-size: 100px;">Криптография древнего мира</h1>
      <p class="lead">
        Как в древности скрывали сообщения, защищали знания и передавали тайны в эпоху войн,
        дипломатии и борьбы за власть.
      </p>
      <p style="max-width: 1000px;">Подготовили студенты группы БИВТ-25-9 Кацадзе Д.М., Федярин М.О. Голышев Ю.А.</p>
    </header>

    <section class="section reveal">
      <h2>Содержание</h2>
      <ul class="bullets">
        <li>Цель работы</li>
        <li>Задачи</li>
        <li>Исследование</li>
        <li>Продукт (видео)</li>
      </ul>
    </section>

    <section class="section reveal">
      <h2>Цель работы</h2>
      <p style="font-size: 30px;">
        Исследовать ранние способы шифрования в древних цивилизациях и показать, почему возникла
        необходимость защищать информацию.
      </p>
    </section>

    <section class="section reveal">
      <h2>Задачи</h2>
      <ul class="bullets">
        <li>Собрать исторические примеры древних шифров и тайнописи.</li>
        <li>Объяснить принципы шифрования понятным языком.</li>
        <li>Показать практическое применение в истории.</li>
        <li>Подготовить итоговый продукт в формате видео.</li>
      </ul>
    </section>

    <section class="section section--research reveal">
      <h2>Исследование</h2>
      <div class="research-split">
        <div class="research-split__text">
          <p>
            Чтобы изучить тему наиболее подробно, мы посетили музей криптографии, где собрали
            информацию о:
          </p>
          <ul class="bullets bullets--research">
            <li>появлении и развитии криптографии;</li>
            <li>методах шифрования информации в древнем мире.</li>
          </ul>
        </div>
        <figure class="research-split__photo" aria-label="Фото из музея криптографии">
          <img :src="museumPhotoUrl" alt="Музей криптографии" width="1896" height="1048" loading="lazy" />
        </figure>
      </div>
    </section>

    <section class="section reveal">
      <h2>Продукт проекта: видео</h2>
      <div class="video-wrap">
        <video :src="videoUrl" controls preload="metadata">
          Ваш браузер не поддерживает видео.
        </video>
      </div>
    </section>

    <section class="section section--final reveal">
      <h2>Спасибо за внимание!</h2>
    </section>
  </div>
</template>
