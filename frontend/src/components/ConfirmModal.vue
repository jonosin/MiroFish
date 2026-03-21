<template>
  <Teleport to="body">
    <Transition name="confirm-modal">
      <div v-if="visible" class="confirm-overlay" @click.self="onCancel">
        <div class="confirm-box">
          <div class="confirm-icon">⚠</div>
          <div class="confirm-title">{{ title }}</div>
          <div class="confirm-message">{{ message }}</div>
          <div class="confirm-note" v-if="note">{{ note }}</div>
          <div class="confirm-actions">
            <button class="confirm-btn cancel" @click="onCancel">Cancel</button>
            <button class="confirm-btn proceed" @click="onConfirm">Confirm</button>
          </div>
        </div>
      </div>
    </Transition>
  </Teleport>
</template>

<script setup>
defineProps({
  visible: { type: Boolean, required: true },
  title: { type: String, default: 'Confirm Action' },
  message: { type: String, default: 'Are you sure you want to proceed?' },
  note: { type: String, default: '' }
})

const emit = defineEmits(['confirm', 'cancel'])
const onConfirm = () => emit('confirm')
const onCancel = () => emit('cancel')
</script>

<style scoped>
.confirm-overlay {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.75);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 9999;
}

.confirm-box {
  background: var(--bg-elevated);
  border: 1px solid var(--border-strong);
  padding: 32px 28px 24px;
  min-width: 340px;
  max-width: 420px;
  text-align: center;
}

.confirm-icon {
  font-size: 1.8rem;
  color: var(--accent);
  margin-bottom: 12px;
}

.confirm-title {
  font-family: var(--font-mono);
  font-size: 0.85rem;
  font-weight: 600;
  color: var(--text-primary);
  letter-spacing: 1px;
  text-transform: uppercase;
  margin-bottom: 12px;
}

.confirm-message {
  font-size: 0.85rem;
  color: var(--text-secondary);
  line-height: 1.6;
  margin-bottom: 8px;
}

.confirm-note {
  font-family: var(--font-mono);
  font-size: 0.72rem;
  color: var(--accent);
  margin-bottom: 20px;
  padding: 6px 10px;
  border: 1px solid rgba(255, 69, 0, 0.25);
  background: rgba(255, 69, 0, 0.06);
}

.confirm-actions {
  display: flex;
  gap: 10px;
  margin-top: 20px;
}

.confirm-btn {
  flex: 1;
  padding: 10px;
  font-family: var(--font-mono);
  font-size: 0.78rem;
  letter-spacing: 1px;
  text-transform: uppercase;
  cursor: pointer;
  border: 1px solid;
  transition: all 0.2s ease;
}

.confirm-btn.cancel {
  background: transparent;
  border-color: var(--border-strong);
  color: var(--text-secondary);
}

.confirm-btn.cancel:hover {
  border-color: var(--text-secondary);
  color: var(--text-primary);
}

.confirm-btn.proceed {
  background: var(--accent);
  border-color: var(--accent);
  color: #fff;
  font-weight: 600;
}

.confirm-btn.proceed:hover {
  background: var(--accent-dim);
  border-color: var(--accent-dim);
}

.confirm-modal-enter-active,
.confirm-modal-leave-active {
  transition: opacity 0.2s ease;
}

.confirm-modal-enter-from,
.confirm-modal-leave-to {
  opacity: 0;
}
</style>
