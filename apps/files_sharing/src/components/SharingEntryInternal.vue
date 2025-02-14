<template>
	<ul>
		<SharingEntrySimple ref="shareEntrySimple"
			class="sharing-entry__internal"
			:title="t('files_sharing', 'Internal link')"
			:subtitle="internalLinkSubtitle">
			<template #avatar>
				<div class="avatar-external icon-external-white" />
			</template>

			<NcActionButton :title="copyLinkTooltip"
				:aria-label="copyLinkTooltip"
				:icon="copied && copySuccess ? 'icon-checkmark-color' : 'icon-clippy'"
				@click="copyLink" />
		</SharingEntrySimple>
	</ul>
</template>

<script>
import { generateUrl } from '@nextcloud/router'
import { showSuccess } from '@nextcloud/dialogs'
import NcActionButton from '@nextcloud/vue/dist/Components/NcActionButton.js'
import SharingEntrySimple from './SharingEntrySimple.vue'

export default {
	name: 'SharingEntryInternal',

	components: {
		NcActionButton,
		SharingEntrySimple,
	},

	props: {
		fileInfo: {
			type: Object,
			default: () => {},
			required: true,
		},
	},

	data() {
		return {
			copied: false,
			copySuccess: false,
		}
	},

	computed: {
		/**
		 * Get the internal link to this file id
		 *
		 * @return {string}
		 */
		internalLink() {
			return window.location.protocol + '//' + window.location.host + generateUrl('/f/') + this.fileInfo.id
		},

		/**
		 * Tooltip message
		 *
		 * @return {string}
		 */
		copyLinkTooltip() {
			if (this.copied) {
				if (this.copySuccess) {
					return ''
				}
				return t('files_sharing', 'Cannot copy, please copy the link manually')
			}
			return t('files_sharing', 'Copy internal link to clipboard')
		},

		internalLinkSubtitle() {
			if (this.fileInfo.type === 'dir') {
				return t('files_sharing', 'Only works for people with access to this folder')
			}
			return t('files_sharing', 'Only works for people with access to this file')
		},
	},

	methods: {
		async copyLink() {
			try {
				await navigator.clipboard.writeText(this.internalLink)
				showSuccess(t('files_sharing', 'Link copied'))
				this.$refs.shareEntrySimple.$refs.actionsComponent.$el.focus()
				this.copySuccess = true
				this.copied = true
			} catch (error) {
				this.copySuccess = false
				this.copied = true
				console.error(error)
			} finally {
				setTimeout(() => {
					this.copySuccess = false
					this.copied = false
				}, 4000)
			}
		},
	},
}
</script>

<style lang="scss" scoped>
.sharing-entry__internal {
	.avatar-external {
		width: 32px;
		height: 32px;
		line-height: 32px;
		font-size: 18px;
		background-color: var(--color-text-maxcontrast);
		border-radius: 50%;
		flex-shrink: 0;
	}
	.icon-checkmark-color {
		opacity: 1;
	}
}
</style>
