<template>
  <div 
    class="history-database"
    :class="{ 'no-projects': projects.length === 0 && !loading }"
    ref="historyContainer"
  >
    <!-- 背景装饰：技术网格线（只在有项目时显示） -->
    <div v-if="projects.length > 0 || loading" class="tech-grid-bg">
      <div class="grid-pattern"></div>
      <div class="gradient-overlay"></div>
    </div>

    <!-- Title area -->
    <div class="section-header">
      <div class="section-line"></div>
      <span class="section-title">History</span>
      <div class="section-line"></div>
    </div>

    <!-- Cards container (shown only when projects exist) -->
    <div v-if="projects.length > 0" class="cards-container" :class="{ expanded: isExpanded }" :style="containerStyle">
      <div 
        v-for="(project, index) in projects" 
        :key="project.simulation_id"
        class="project-card"
        :class="{ expanded: isExpanded, hovering: hoveringCard === index }"
        :style="getCardStyle(index)"
        @mouseenter="hoveringCard = index"
        @mouseleave="hoveringCard = null"
        @click="navigateToProject(project)"
      >
        <!-- Card header: simulation_id and feature availability -->
        <div class="card-header">
          <span class="card-id">{{ formatSimulationId(project.simulation_id) }}</span>
          <div class="card-status-icons">
            <span 
              class="status-icon" 
              :class="{ available: project.project_id, unavailable: !project.project_id }"
              title="Graph Build"
            >◇</span>
            <span
              class="status-icon"
              :class="{ available: project.profiles_count > 0, unavailable: !project.profiles_count }"
              title="Env Setup"
            >◈</span>
            <span
              class="status-icon"
              :class="{ available: project.report_id, unavailable: !project.report_id }"
              title="Report"
            >◆</span>
          </div>
        </div>

        <!-- Files list area -->
        <div class="card-files-wrapper">
          <!-- Corner decoration - viewfinder style -->
          <div class="corner-mark top-left-only"></div>

          <!-- Files list -->
          <div class="files-list" v-if="project.files && project.files.length > 0">
            <div
              v-for="(file, fileIndex) in project.files.slice(0, 3)"
              :key="fileIndex"
              class="file-item"
            >
              <span class="file-tag" :class="getFileType(file.filename)">{{ getFileTypeLabel(file.filename) }}</span>
              <span class="file-name">{{ truncateFilename(file.filename, 20) }}</span>
            </div>
            <!-- Show hint if more files -->
            <div v-if="project.files.length > 3" class="files-more">
              +{{ project.files.length - 3 }} files
            </div>
          </div>
          <!-- Placeholder when no files -->
          <div class="files-empty" v-else>
            <span class="empty-file-icon">◇</span>
            <span class="empty-file-text">No files</span>
          </div>
        </div>

        <!-- Card title (first 20 characters of simulation requirement) -->
        <h3 class="card-title">{{ getSimulationTitle(project.simulation_requirement) }}</h3>

        <!-- Card description (full simulation requirement display) -->
        <p class="card-desc">{{ truncateText(project.simulation_requirement, 55) }}</p>

        <!-- Card footer -->
        <div class="card-footer">
          <div class="card-datetime">
            <span class="card-date">{{ formatDate(project.created_at) }}</span>
            <span class="card-time">{{ formatTime(project.created_at) }}</span>
          </div>
          <span class="card-progress" :class="getProgressClass(project)">
            <span class="status-dot">●</span> {{ formatRounds(project) }}
          </span>
        </div>

        <!-- Env status badge -->
        <div class="card-env-badge" :class="project.profiles_count > 0 ? 'env-ready' : 'env-not-set'">
          {{ project.profiles_count > 0 ? 'Env Ready' : 'Env Not Set Up' }}
        </div>
        
        <!-- Bottom decoration line (expands on hover) -->
        <div class="card-bottom-line"></div>
      </div>
    </div>

    <!-- Loading state -->
    <div v-if="loading" class="loading-state">
      <span class="loading-spinner"></span>
      <span class="loading-text">Loading...</span>
    </div>

    <!-- History replay modal -->
    <Teleport to="body">
      <Transition name="modal">
        <div v-if="selectedProject" class="modal-overlay" @click.self="closeModal">
          <div class="modal-content">
            <!-- Modal header -->
            <div class="modal-header">
              <div class="modal-title-section">
                <span class="modal-id">{{ formatSimulationId(selectedProject.simulation_id) }}</span>
                <span class="modal-progress" :class="getProgressClass(selectedProject)">
                  <span class="status-dot">●</span> {{ formatRounds(selectedProject) }}
                </span>
                <span class="modal-create-time">{{ formatDate(selectedProject.created_at) }} {{ formatTime(selectedProject.created_at) }}</span>
              </div>
              <button class="modal-close" @click="closeModal">×</button>
            </div>

            <!-- Modal content -->
            <div class="modal-body">
              <!-- Simulation requirement -->
              <div class="modal-section">
                <div class="modal-label">Simulation Requirement</div>
                <div class="modal-requirement">{{ selectedProject.simulation_requirement || 'None' }}</div>
              </div>

              <!-- Files list -->
              <div class="modal-section">
                <div class="modal-label">Files</div>
                <div class="modal-files" v-if="selectedProject.files && selectedProject.files.length > 0">
                  <div v-for="(file, index) in selectedProject.files" :key="index" class="modal-file-item">
                    <span class="file-tag" :class="getFileType(file.filename)">{{ getFileTypeLabel(file.filename) }}</span>
                    <span class="modal-file-name">{{ file.filename }}</span>
                  </div>
                </div>
                <div class="modal-empty" v-else>No files linked</div>
              </div>
            </div>

            <!-- History replay divider -->
            <div class="modal-divider">
              <span class="divider-line"></span>
              <span class="divider-text">Replay</span>
              <span class="divider-line"></span>
            </div>

            <!-- Navigation buttons -->
            <div class="modal-actions">
              <button 
                class="modal-btn btn-project" 
                @click="goToProject"
                :disabled="!selectedProject.project_id"
              >
                <span class="btn-step">Step1</span>
                <span class="btn-icon">◇</span>
                <span class="btn-text">Graph Build</span>
              </button>
              <button
                class="modal-btn btn-simulation"
                @click="goToSimulation"
              >
                <span class="btn-step">Step2</span>
                <span class="btn-icon">◈</span>
                <span class="btn-text">Env Setup</span>
              </button>
              <button
                class="modal-btn btn-report"
                @click="goToReport"
                :disabled="!selectedProject.report_id"
              >
                <span class="btn-step">Step4</span>
                <span class="btn-icon">◆</span>
                <span class="btn-text">Report</span>
              </button>
            </div>
            <!-- Non-replayable hint -->
            <div class="modal-playback-hint">
              <span class="hint-text">Step 3 (Simulate) and Step 5 (Deep Interact) must be launched live — history replay not supported</span>
            </div>
          </div>
        </div>
      </Transition>
    </Teleport>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted, onActivated, watch, nextTick } from 'vue'
import { useRouter, useRoute } from 'vue-router'
import { getSimulationHistory } from '../api/simulation'

const router = useRouter()
const route = useRoute()

// 状态
const projects = ref([])
const loading = ref(true)
const isExpanded = ref(true)
const hoveringCard = ref(null)
const historyContainer = ref(null)
const selectedProject = ref(null)  // 当前选中的项目（用于弹窗）
let observer = null
let isAnimating = false  // 动画锁，防止闪烁
let expandDebounceTimer = null  // 防抖定时器
let pendingState = null  // 记录待执行的目标状态

// Card layout config - wider aspect ratio
const CARDS_PER_ROW = 4
const CARD_WIDTH = 280
const CARD_HEIGHT = 280
const CARD_GAP = 24

// Dynamically compute container height style
const containerStyle = computed(() => {
  if (!isExpanded.value) {
    // Collapsed state: fixed height
    return { minHeight: '420px' }
  }

  // Expanded state: dynamic height based on card count
  const total = projects.value.length
  if (total === 0) {
    return { minHeight: '280px' }
  }

  const rows = Math.ceil(total / CARDS_PER_ROW)
  // Calculate actual required height: rows * card height + (rows-1) * gap + bottom spacing
  const expandedHeight = rows * CARD_HEIGHT + (rows - 1) * CARD_GAP + 10

  return { minHeight: `${expandedHeight}px` }
})

// Get card styles
const getCardStyle = (index) => {
  const total = projects.value.length

  if (isExpanded.value) {
    // Expanded state: grid layout
    const transition = 'transform 700ms cubic-bezier(0.23, 1, 0.32, 1), opacity 700ms cubic-bezier(0.23, 1, 0.32, 1), box-shadow 0.3s ease, border-color 0.3s ease'

    const col = index % CARDS_PER_ROW
    const row = Math.floor(index / CARDS_PER_ROW)

    // Calculate current row card count, ensure centered rows
    const currentRowStart = row * CARDS_PER_ROW
    const currentRowCards = Math.min(CARDS_PER_ROW, total - currentRowStart)

    const rowWidth = currentRowCards * CARD_WIDTH + (currentRowCards - 1) * CARD_GAP

    const startX = -(rowWidth / 2) + (CARD_WIDTH / 2)
    const colInRow = index % CARDS_PER_ROW
    const x = startX + colInRow * (CARD_WIDTH + CARD_GAP)

    // Expand downward, increase spacing from title
    const y = 20 + row * (CARD_HEIGHT + CARD_GAP)

    return {
      transform: `translate(${x}px, ${y}px) rotate(0deg) scale(1)`,
      zIndex: 100 + index,
      opacity: 1,
      transition: transition
    }
  } else {
    // Collapsed state: fan stacking
    const transition = 'transform 700ms cubic-bezier(0.23, 1, 0.32, 1), opacity 700ms cubic-bezier(0.23, 1, 0.32, 1), box-shadow 0.3s ease, border-color 0.3s ease'

    const centerIndex = (total - 1) / 2
    const offset = index - centerIndex

    const x = offset * 35
    // Adjust starting position, close to title but with proper spacing
    const y = 25 + Math.abs(offset) * 8
    const r = offset * 3
    const s = 0.95 - Math.abs(offset) * 0.05

    return {
      transform: `translate(${x}px, ${y}px) rotate(${r}deg) scale(${s})`,
      zIndex: 10 + index,
      opacity: 1,
      transition: transition
    }
  }
}

// Get style class based on actual simulation status
const getProgressClass = (simulation) => {
  const status = simulation.status || ''
  const runnerStatus = simulation.runner_status || ''
  const current = simulation.current_round || 0
  const total = simulation.total_rounds || 0

  if (status === 'error') return 'error'
  if (status === 'stopped' || runnerStatus === 'stopped') return 'stopped'
  if (status === 'preparing') return 'preparing'
  if (status === 'created') return 'not-started'
  if (total > 0 && current >= total && (status === 'completed' || runnerStatus === 'completed')) return 'completed'
  if (current > 0 && current < total) return 'in-progress'
  if (total === 0 && status === 'ready') return 'ready'
  return 'not-started'
}

// Format date (show date part only)
const formatDate = (dateStr) => {
  if (!dateStr) return ''
  try {
    const date = new Date(dateStr)
    return date.toISOString().slice(0, 10)
  } catch {
    return dateStr?.slice(0, 10) || ''
  }
}

// Format time (show hours:minutes)
const formatTime = (dateStr) => {
  if (!dateStr) return ''
  try {
    const date = new Date(dateStr)
    const hours = date.getHours().toString().padStart(2, '0')
    const minutes = date.getMinutes().toString().padStart(2, '0')
    return `${hours}:${minutes}`
  } catch {
    return ''
  }
}

// Truncate text
const truncateText = (text, maxLength) => {
  if (!text) return ''
  return text.length > maxLength ? text.slice(0, maxLength) + '...' : text
}

// Generate title from simulation requirement (first 20 characters)
const getSimulationTitle = (requirement) => {
  if (!requirement) return 'Unnamed Simulation'
  const title = requirement.slice(0, 20)
  return requirement.length > 20 ? title + '...' : title
}

// Format simulation_id display (first 6 characters)
const formatSimulationId = (simulationId) => {
  if (!simulationId) return 'SIM_UNKNOWN'
  const prefix = simulationId.replace('sim_', '').slice(0, 6)
  return `SIM_${prefix.toUpperCase()}`
}

// Format rounds display based on actual status
const formatRounds = (simulation) => {
  const status = simulation.status || ''
  const runnerStatus = simulation.runner_status || ''
  const current = simulation.current_round || 0
  const total = simulation.total_rounds || 0

  if (status === 'error') return 'Error'
  if (status === 'stopped' || runnerStatus === 'stopped') {
    if (total > 0) return `Stopped (${current}/${total})`
    return 'Stopped'
  }
  if (status === 'preparing') return 'Preparing...'
  if (status === 'created') return 'Not started'
  if (total > 0 && current >= total) return `${current}/${total} rounds`
  if (total > 0) return `${current}/${total} rounds`
  if (status === 'ready') return 'Ready'
  return 'Not started'
}

// Get file type (for styling)
const getFileType = (filename) => {
  if (!filename) return 'other'
  const ext = filename.split('.').pop()?.toLowerCase()
  const typeMap = {
    'pdf': 'pdf',
    'doc': 'doc', 'docx': 'doc',
    'xls': 'xls', 'xlsx': 'xls', 'csv': 'xls',
    'ppt': 'ppt', 'pptx': 'ppt',
    'txt': 'txt', 'md': 'txt', 'json': 'code',
    'jpg': 'img', 'jpeg': 'img', 'png': 'img', 'gif': 'img',
    'zip': 'zip', 'rar': 'zip', '7z': 'zip'
  }
  return typeMap[ext] || 'other'
}

// Get file type label text
const getFileTypeLabel = (filename) => {
  if (!filename) return 'FILE'
  const ext = filename.split('.').pop()?.toUpperCase()
  return ext || 'FILE'
}

// Truncate filename (keep extension)
const truncateFilename = (filename, maxLength) => {
  if (!filename) return 'Unknown file'
  if (filename.length <= maxLength) return filename

  const ext = filename.includes('.') ? '.' + filename.split('.').pop() : ''
  const nameWithoutExt = filename.slice(0, filename.length - ext.length)
  const truncatedName = nameWithoutExt.slice(0, maxLength - ext.length - 3) + '...'
  return truncatedName + ext
}

// Open project details modal
const navigateToProject = (simulation) => {
  selectedProject.value = simulation
}

// Close modal
const closeModal = () => {
  selectedProject.value = null
}

// Navigate to graph build page (Project)
const goToProject = () => {
  if (selectedProject.value?.project_id) {
    router.push({
      name: 'Process',
      params: { projectId: selectedProject.value.project_id }
    })
    closeModal()
  }
}

// Navigate to env setup page (Simulation)
const goToSimulation = () => {
  if (selectedProject.value?.simulation_id) {
    router.push({
      name: 'Simulation',
      params: { simulationId: selectedProject.value.simulation_id }
    })
    closeModal()
  }
}

// Navigate to report page (Report)
const goToReport = () => {
  if (selectedProject.value?.report_id) {
    router.push({
      name: 'Report',
      params: { reportId: selectedProject.value.report_id }
    })
    closeModal()
  }
}

// Load history projects
const loadHistory = async () => {
  try {
    loading.value = true
    const response = await getSimulationHistory(20)
    if (response.success) {
      projects.value = response.data || []
    }
  } catch (error) {
    console.error('Failed to load history projects:', error)
    projects.value = []
  } finally {
    loading.value = false
  }
}

// Initialize IntersectionObserver
const initObserver = () => {
  if (observer) {
    observer.disconnect()
  }

  observer = new IntersectionObserver(
    (entries) => {
      entries.forEach((entry) => {
        const shouldExpand = entry.isIntersecting

        // Update pending target state (always record latest regardless of animation)
        pendingState = shouldExpand

        // Clear previous debounce timer (new scroll intent overrides old)
        if (expandDebounceTimer) {
          clearTimeout(expandDebounceTimer)
          expandDebounceTimer = null
        }

        // If animating, only record state, handle after animation completes
        if (isAnimating) return

        // If target state matches current state, no action needed
        if (shouldExpand === isExpanded.value) {
          pendingState = null
          return
        }

        // Use debounce delay for state switch, prevent rapid flickering
        // Shorter delay on expand (50ms), longer on collapse (200ms) for stability
        const delay = shouldExpand ? 50 : 200

        expandDebounceTimer = setTimeout(() => {
          // Check if still animating
          if (isAnimating) return

          // Check if pending state still needs execution (may be overridden by later scroll)
          if (pendingState === null || pendingState === isExpanded.value) return

          // Set animation lock
          isAnimating = true
          isExpanded.value = pendingState
          pendingState = null

          // Unlock after animation completes, check for pending state changes
          setTimeout(() => {
            isAnimating = false

            // After animation, check for new pending state
            if (pendingState !== null && pendingState !== isExpanded.value) {
              // Delay slightly to avoid too-fast switching
              expandDebounceTimer = setTimeout(() => {
                if (pendingState !== null && pendingState !== isExpanded.value) {
                  isAnimating = true
                  isExpanded.value = pendingState
                  pendingState = null
                  setTimeout(() => {
                    isAnimating = false
                  }, 750)
                }
              }, 100)
            }
          }, 750)
        }, delay)
      })
    },
    {
      // Use multiple thresholds for smoother detection
      threshold: [0.1, 0.2, 0.3],
      rootMargin: '0px 0px 0px 0px'
    }
  )

  // Start observing
  if (historyContainer.value) {
    observer.observe(historyContainer.value)
  }
}

// Watch route changes, reload data when returning home
watch(() => route.path, (newPath) => {
  if (newPath === '/') {
    loadHistory()
  }
})

onMounted(async () => {
  // Ensure DOM rendering completes before loading data
  await nextTick()
  await loadHistory()

  // Initialize observer after DOM renders
  setTimeout(() => {
    initObserver()
  }, 100)
})

// Reload data on component activation if using keep-alive
onActivated(() => {
  loadHistory()
})

onUnmounted(() => {
  // Clean up Intersection Observer
  if (observer) {
    observer.disconnect()
    observer = null
  }
  // Clean up debounce timer
  if (expandDebounceTimer) {
    clearTimeout(expandDebounceTimer)
    expandDebounceTimer = null
  }
})
</script>

<style scoped>
/* Container */
.history-database {
  position: relative;
  width: 100%;
  min-height: 280px;
  margin-top: 40px;
  padding: 35px 0 40px;
  overflow: visible;
}

/* Simplified display when no projects */
.history-database.no-projects {
  min-height: auto;
  padding: 40px 0 20px;
}

/* Tech grid background */
.tech-grid-bg {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  overflow: hidden;
  pointer-events: none;
}

/* CSS background pattern: fixed-spacing square grid */
.grid-pattern {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-image:
    linear-gradient(to right, rgba(255, 255, 255, 0.03) 1px, transparent 1px),
    linear-gradient(to bottom, rgba(255, 255, 255, 0.03) 1px, transparent 1px);
  background-size: 50px 50px;
  background-position: top left;
}

.gradient-overlay {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background:
    linear-gradient(to right, rgba(10, 10, 10, 0.9) 0%, transparent 15%, transparent 85%, rgba(10, 10, 10, 0.9) 100%),
    linear-gradient(to bottom, rgba(10, 10, 10, 0.8) 0%, transparent 20%, transparent 80%, rgba(10, 10, 10, 0.8) 100%);
  pointer-events: none;
}

/* Section header */
.section-header {
  position: relative;
  z-index: 100;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 24px;
  margin-bottom: 24px;
  font-family: var(--font-mono);
  padding: 0 40px;
}

.section-line {
  flex: 1;
  height: 1px;
  background: linear-gradient(90deg, transparent, var(--border-default), transparent);
  max-width: 300px;
}

.section-title {
  font-size: 0.8rem;
  font-weight: 500;
  color: var(--text-muted);
  letter-spacing: 3px;
  text-transform: uppercase;
}

/* Cards container */
.cards-container {
  position: relative;
  display: flex;
  justify-content: center;
  align-items: flex-start;
  padding: 0 40px;
  transition: min-height 700ms cubic-bezier(0.23, 1, 0.32, 1);
  /* min-height dynamically computed by JS, adapts to card count */
}

/* Project card */
.project-card {
  position: absolute;
  width: 280px;
  background: var(--bg-card);
  border: 1px solid var(--border-subtle);
  border-radius: 0;
  padding: 14px;
  cursor: pointer;
  box-shadow: 0 1px 2px 0 rgba(0, 0, 0, 0.3);
  transition: box-shadow 0.3s ease, border-color 0.3s ease, transform 700ms cubic-bezier(0.23, 1, 0.32, 1), opacity 700ms cubic-bezier(0.23, 1, 0.32, 1);
}

.project-card:hover {
  box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.4), 0 4px 6px -2px rgba(0, 0, 0, 0.2);
  border-color: var(--accent);
  z-index: 1000 !important;
}

.project-card.hovering {
  z-index: 1000 !important;
}

/* Card header */
.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 12px;
  padding-bottom: 12px;
  border-bottom: 1px solid var(--border-subtle);
  font-family: var(--font-mono);
  font-size: 0.7rem;
}

.card-id {
  color: var(--text-secondary);
  letter-spacing: 0.5px;
  font-weight: 500;
}

/* Feature status icon group */
.card-status-icons {
  display: flex;
  align-items: center;
  gap: 6px;
}

.status-icon {
  font-size: 0.75rem;
  transition: all 0.2s ease;
  cursor: default;
}

.status-icon.available {
  opacity: 1;
}

/* Feature icon colors */
.status-icon:nth-child(1).available { color: #4a9eff; } /* Graph Build - blue */
.status-icon:nth-child(2).available { color: var(--accent); } /* Env Setup - orange */
.status-icon:nth-child(3).available { color: var(--status-success); } /* Report - green */

.status-icon.unavailable {
  color: var(--border-default);
  opacity: 0.5;
}

/* Rounds progress display */
.card-progress {
  display: flex;
  align-items: center;
  gap: 6px;
  letter-spacing: 0.5px;
  font-weight: 600;
  font-size: 0.65rem;
}

.status-dot {
  font-size: 0.5rem;
}

/* Progress status colors */
.card-progress.completed { color: var(--status-success); }
.card-progress.in-progress { color: var(--accent); }
.card-progress.not-started { color: var(--text-muted); }
.card-progress.stopped { color: var(--status-error); }
.card-progress.error { color: var(--status-error); }
.card-progress.preparing { color: var(--accent); }
.card-progress.ready { color: var(--status-success); }
.card-status.pending { color: var(--text-muted); }

/* Files list area */
.card-files-wrapper {
  position: relative;
  width: 100%;
  min-height: 48px;
  max-height: 110px;
  margin-bottom: 12px;
  padding: 8px 10px;
  background: var(--bg-elevated);
  border-radius: 4px;
  border: 1px solid var(--border-subtle);
  overflow: hidden;
}

.files-list {
  display: flex;
  flex-direction: column;
  gap: 4px;
}

/* More files hint */
.files-more {
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 3px 6px;
  font-family: var(--font-mono);
  font-size: 0.6rem;
  color: var(--text-secondary);
  background: rgba(255, 255, 255, 0.05);
  border-radius: 3px;
  letter-spacing: 0.3px;
}

.file-item {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 4px 6px;
  background: rgba(255, 255, 255, 0.04);
  border-radius: 3px;
  transition: all 0.2s ease;
}

.file-item:hover {
  background: rgba(255, 255, 255, 0.08);
  transform: translateX(2px);
  border-color: var(--border-default);
}

/* File tag styles */
.file-tag {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  height: 16px;
  padding: 0 4px;
  border-radius: 2px;
  font-family: var(--font-mono);
  font-size: 0.55rem;
  font-weight: 600;
  line-height: 1;
  text-transform: uppercase;
  letter-spacing: 0.2px;
  flex-shrink: 0;
  min-width: 28px;
}

/* Dark file type colors */
.file-tag.pdf { background: rgba(166, 90, 90, 0.2); color: #c97a7a; }
.file-tag.doc { background: rgba(90, 126, 166, 0.2); color: #7aa0c9; }
.file-tag.xls { background: rgba(90, 166, 104, 0.2); color: #7ac98c; }
.file-tag.ppt { background: rgba(166, 129, 90, 0.2); color: #c9a07a; }
.file-tag.txt { background: rgba(117, 117, 117, 0.2); color: #999; }
.file-tag.code { background: rgba(129, 90, 166, 0.2); color: #a07ac9; }
.file-tag.img { background: rgba(90, 166, 166, 0.2); color: #7ac9c9; }
.file-tag.zip { background: rgba(166, 155, 90, 0.2); color: #c9bc7a; }
.file-tag.other { background: rgba(107, 114, 128, 0.2); color: var(--text-muted); }

.file-name {
  font-family: var(--font-sans);
  font-size: 0.7rem;
  color: var(--text-secondary);
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  letter-spacing: 0.1px;
}

/* Empty files placeholder */
.files-empty {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  height: 48px;
  color: var(--text-muted);
}

.empty-file-icon {
  font-size: 1rem;
  opacity: 0.5;
}

.empty-file-text {
  font-family: var(--font-mono);
  font-size: 0.7rem;
  letter-spacing: 0.5px;
}

/* File area hover effect */
.project-card:hover .card-files-wrapper {
  border-color: var(--border-default);
  background: var(--bg-elevated);
}

/* Corner decoration */
.corner-mark.top-left-only {
  position: absolute;
  top: 6px;
  left: 6px;
  width: 8px;
  height: 8px;
  border-top: 1.5px solid var(--accent);
  border-left: 1.5px solid var(--accent);
  pointer-events: none;
  z-index: 10;
}

/* Card title */
.card-title {
  font-family: var(--font-sans);
  font-size: 0.9rem;
  font-weight: 700;
  color: var(--text-primary);
  margin: 0 0 6px 0;
  line-height: 1.4;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  transition: color 0.3s ease;
}

.project-card:hover .card-title {
  color: var(--accent);
}

/* Card description */
.card-desc {
  font-family: var(--font-sans);
  font-size: 0.75rem;
  color: var(--text-secondary);
  margin: 0 0 16px 0;
  line-height: 1.5;
  height: 34px;
  overflow: hidden;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
}

/* Card footer */
.card-footer {
  position: relative;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding-top: 12px;
  border-top: 1px solid var(--border-subtle);
  font-family: var(--font-mono);
  font-size: 0.65rem;
  color: var(--text-muted);
  font-weight: 500;
}

/* 日期时间组合 */
.card-datetime {
  display: flex;
  align-items: center;
  gap: 8px;
}

/* 底部轮数进度显示 */
.card-footer .card-progress {
  display: flex;
  align-items: center;
  gap: 6px;
  letter-spacing: 0.5px;
  font-weight: 600;
  font-size: 0.65rem;
}

.card-footer .status-dot {
  font-size: 0.5rem;
}

/* Progress status colors - footer */
.card-footer .card-progress.completed { color: var(--status-success); }
.card-footer .card-progress.in-progress { color: var(--accent); }
.card-footer .card-progress.not-started { color: var(--text-muted); }
.card-footer .card-progress.stopped { color: var(--status-error); }
.card-footer .card-progress.error { color: var(--status-error); }
.card-footer .card-progress.preparing { color: var(--accent); }
.card-footer .card-progress.ready { color: var(--status-success); }

/* Env status badge */
.card-env-badge {
  font-family: var(--font-mono);
  font-size: 10px;
  letter-spacing: 0.5px;
  padding: 2px 8px;
  border-radius: 2px;
  margin-top: 6px;
  display: inline-block;
}
.card-env-badge.env-ready {
  color: var(--status-success);
  background: rgba(16, 185, 129, 0.08);
  border: 1px solid rgba(16, 185, 129, 0.2);
}
.card-env-badge.env-not-set {
  color: var(--text-muted);
  background: rgba(255, 255, 255, 0.04);
  border: 1px solid var(--border-subtle);
}

/* Bottom accent line */
.card-bottom-line {
  position: absolute;
  bottom: 0;
  left: 0;
  height: 2px;
  width: 0;
  background-color: var(--accent);
  transition: width 0.5s cubic-bezier(0.23, 1, 0.32, 1);
  z-index: 20;
}

.project-card:hover .card-bottom-line {
  width: 100%;
}

/* Empty / loading state */
.empty-state, .loading-state {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 14px;
  padding: 48px;
  color: var(--text-muted);
}

.empty-icon {
  font-size: 2rem;
  opacity: 0.5;
}

.loading-spinner {
  width: 24px;
  height: 24px;
  border: 2px solid var(--border-default);
  border-top-color: var(--accent);
  border-radius: 50%;
  animation: spin 0.8s linear infinite;
}

@keyframes spin {
  to { transform: rotate(360deg); }
}

/* 响应式 */
@media (max-width: 1200px) {
  .project-card {
    width: 240px;
  }
}

@media (max-width: 768px) {
  .cards-container {
    padding: 0 20px;
  }
  .project-card {
    width: 200px;
  }
}

/* ===== History replay modal ===== */
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.7);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 9999;
  backdrop-filter: blur(4px);
}

.modal-content {
  background: var(--bg-surface);
  width: 560px;
  max-width: 90vw;
  max-height: 85vh;
  overflow-y: auto;
  border: 1px solid var(--border-default);
  border-radius: 8px;
  box-shadow: 0 20px 25px -5px rgba(0, 0, 0, 0.5), 0 10px 10px -5px rgba(0, 0, 0, 0.3);
}

/* 动画过渡 */
.modal-enter-active,
.modal-leave-active {
  transition: opacity 0.3s ease;
}

.modal-enter-from,
.modal-leave-to {
  opacity: 0;
}

.modal-enter-active .modal-content {
  transition: all 0.3s cubic-bezier(0.34, 1.56, 0.64, 1);
}

.modal-leave-active .modal-content {
  transition: all 0.2s ease-in;
}

.modal-enter-from .modal-content {
  transform: scale(0.95) translateY(10px);
  opacity: 0;
}

.modal-leave-to .modal-content {
  transform: scale(0.95) translateY(10px);
  opacity: 0;
}

/* Modal header */
.modal-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 20px 32px;
  border-bottom: 1px solid var(--border-subtle);
  background: var(--bg-surface);
}

.modal-title-section {
  display: flex;
  align-items: center;
  gap: 16px;
}

.modal-id {
  font-family: var(--font-mono);
  font-size: 1rem;
  font-weight: 600;
  color: var(--text-primary);
  letter-spacing: 0.5px;
}

.modal-progress {
  display: flex;
  align-items: center;
  gap: 6px;
  font-family: var(--font-mono);
  font-size: 0.75rem;
  font-weight: 600;
  padding: 4px 8px;
  border-radius: 4px;
  background: var(--bg-elevated);
}

.modal-progress.completed { color: var(--status-success); background: rgba(34, 197, 94, 0.1); }
.modal-progress.in-progress { color: var(--accent); background: rgba(255, 69, 0, 0.1); }
.modal-progress.not-started { color: var(--text-muted); background: var(--bg-elevated); }
.modal-progress.stopped { color: var(--status-error); background: rgba(239, 68, 68, 0.1); }
.modal-progress.error { color: var(--status-error); background: rgba(239, 68, 68, 0.1); }
.modal-progress.preparing { color: var(--accent); background: rgba(255, 69, 0, 0.1); }
.modal-progress.ready { color: var(--status-success); background: rgba(34, 197, 94, 0.1); }

.modal-create-time {
  font-family: var(--font-mono);
  font-size: 0.75rem;
  color: var(--text-muted);
  letter-spacing: 0.3px;
}

.modal-close {
  width: 32px;
  height: 32px;
  border: none;
  background: transparent;
  font-size: 1.5rem;
  color: var(--text-muted);
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.2s ease;
  border-radius: 6px;
}

.modal-close:hover {
  background: var(--bg-elevated);
  color: var(--text-primary);
}

/* Modal body */
.modal-body {
  padding: 24px 32px;
}

.modal-section {
  margin-bottom: 24px;
}

.modal-section:last-child {
  margin-bottom: 0;
}

.modal-label {
  font-family: var(--font-mono);
  font-size: 0.75rem;
  color: var(--text-secondary);
  text-transform: uppercase;
  letter-spacing: 1px;
  margin-bottom: 10px;
  font-weight: 500;
}

.modal-requirement {
  font-size: 0.95rem;
  color: var(--text-primary);
  line-height: 1.6;
  padding: 16px;
  background: var(--bg-elevated);
  border: 1px solid var(--border-subtle);
  border-radius: 8px;
}

.modal-files {
  display: flex;
  flex-direction: column;
  gap: 10px;
  max-height: 200px;
  overflow-y: auto;
  padding-right: 4px;
}

/* Custom scrollbar */
.modal-files::-webkit-scrollbar {
  width: 4px;
}

.modal-files::-webkit-scrollbar-track {
  background: var(--bg-elevated);
  border-radius: 2px;
}

.modal-files::-webkit-scrollbar-thumb {
  background: var(--border-default);
  border-radius: 2px;
}

.modal-files::-webkit-scrollbar-thumb:hover {
  background: var(--border-strong);
}

.modal-file-item {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 10px 14px;
  background: var(--bg-elevated);
  border: 1px solid var(--border-subtle);
  border-radius: 6px;
  transition: all 0.2s ease;
}

.modal-file-item:hover {
  border-color: var(--border-default);
  box-shadow: 0 1px 2px 0 rgba(0, 0, 0, 0.2);
}

.modal-file-name {
  font-size: 0.85rem;
  color: var(--text-secondary);
  flex: 1;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

.modal-empty {
  font-size: 0.85rem;
  color: var(--text-muted);
  padding: 16px;
  background: var(--bg-elevated);
  border: 1px dashed var(--border-subtle);
  border-radius: 6px;
  text-align: center;
}

/* Replay divider */
.modal-divider {
  display: flex;
  align-items: center;
  gap: 16px;
  padding: 10px 32px 0;
  background: var(--bg-surface);
}

.divider-line {
  flex: 1;
  height: 1px;
  background: linear-gradient(90deg, transparent, var(--border-default), transparent);
}

.divider-text {
  font-family: var(--font-mono);
  font-size: 0.7rem;
  color: var(--text-muted);
  letter-spacing: 2px;
  text-transform: uppercase;
  white-space: nowrap;
}

/* Navigation buttons */
.modal-actions {
  display: flex;
  gap: 16px;
  padding: 20px 32px;
  background: var(--bg-surface);
}

.modal-btn {
  flex: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 8px;
  padding: 16px;
  border: 1px solid var(--border-subtle);
  border-radius: 8px;
  background: var(--bg-elevated);
  cursor: pointer;
  transition: all 0.2s ease;
  position: relative;
  overflow: hidden;
}

.modal-btn:hover:not(:disabled) {
  border-color: var(--accent);
  transform: translateY(-2px);
  box-shadow: 0 4px 6px -1px rgba(255, 69, 0, 0.2);
}

.modal-btn:disabled {
  opacity: 0.5;
  cursor: not-allowed;
  background: var(--bg-elevated);
}

.btn-step {
  font-family: var(--font-mono);
  font-size: 0.6rem;
  font-weight: 500;
  color: var(--text-muted);
  letter-spacing: 0.5px;
  text-transform: uppercase;
}

.btn-icon {
  font-size: 1.4rem;
  line-height: 1;
  transition: color 0.2s ease;
}

.btn-text {
  font-family: var(--font-mono);
  font-size: 0.75rem;
  font-weight: 600;
  letter-spacing: 0.5px;
  color: var(--text-secondary);
}

.modal-btn.btn-project .btn-icon { color: #4a9eff; }
.modal-btn.btn-simulation .btn-icon { color: var(--accent); }
.modal-btn.btn-report .btn-icon { color: var(--status-success); }

.modal-btn:hover:not(:disabled) .btn-text {
  color: var(--text-primary);
}

/* Playback hint */
.modal-playback-hint {
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 0 32px 20px;
  background: var(--bg-surface);
}

.hint-text {
  font-family: var(--font-mono);
  font-size: 0.7rem;
  color: var(--text-muted);
  letter-spacing: 0.3px;
  text-align: center;
  line-height: 1.5;
}
</style>
