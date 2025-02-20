<template>
	<Dialog v-model="show" :options="{ size: '6xl' }">
		<template #body>
			<div class="flex h-[calc(100vh_-_8rem)]">
				<div class="flex w-52 shrink-0 flex-col bg-gray-50 p-2">
					<h1 class="mb-3 px-2 pt-2 text-lg font-semibold">
						{{ __('Settings') }}
					</h1>
					<div v-for="tab in tabs">
						<div
							v-if="!tab.hideLabel"
							class="mb-2 mt-3 flex cursor-pointer gap-1.5 px-1 text-base font-medium text-gray-600 transition-all duration-300 ease-in-out"
						>
							<span>{{ __(tab.label) }}</span>
						</div>
						<nav class="space-y-1">
							<SidebarLink
								v-for="item in tab.items"
								:link="item"
								class="w-full"
								:class="
									activeTab?.label == item.label
										? 'bg-white shadow-sm'
										: 'hover:bg-gray-100'
								"
								@click="activeTab = item"
							/>
						</nav>
					</div>
				</div>
				<div class="flex flex-1 flex-col overflow-y-auto">
					<SettingDetails
						v-if="activeTab && data.doc"
						:fields="activeTab.fields"
						:data="data"
					/>
				</div>
			</div>
		</template>
	</Dialog>
</template>
<script setup>
import { Dialog, createDocumentResource } from 'frappe-ui'
import { ref, computed, watch } from 'vue'
import SettingDetails from '../SettingDetails.vue'
import SidebarLink from '@/components/SidebarLink.vue'

const show = defineModel()
const doctype = ref('LMS Settings')
const activeTab = ref(null)

const data = createDocumentResource({
	doctype: doctype.value,
	name: doctype.value,
	fields: ['*'],
	cache: doctype.value,
	auto: true,
})

const tabs = computed(() => {
	let _tabs = [
		{
			label: 'Settings',
			hideLabel: true,
			items: [
				{
					label: 'Payment Gateway',
					icon: 'DollarSign',
					fields: [
						{
							label: 'Razorpay Key',
							name: 'razorpay_key',
							type: 'text',
						},
						{
							label: 'Razorpay Secret',
							name: 'razorpay_secret',
							type: 'password',
						},
						{
							label: 'Default Currency',
							name: 'default_currency',
							type: 'Link',
							doctype: 'Currency',
						},
						{
							type: 'Column Break',
						},
						{
							label: 'Apply GST for India',
							name: 'apply_gst',
							type: 'checkbox',
						},
						{
							label: 'Show USD equivalent amount',
							name: 'show_usd_equivalent',
							type: 'checkbox',
						},
						{
							label: 'Apply rounding on equivalent amount',
							name: 'apply_rounding',
							type: 'checkbox',
						},
					],
				},
			],
		},
		{
			label: 'Settings',
			hideLabel: true,
			items: [
				{
					label: 'Signup',
					icon: 'LogIn',
					fields: [
						{
							label: 'Show terms of use on signup page',
							name: 'terms_of_use',
							type: 'checkbox',
						},
						{
							label: 'Terms of Use Page',
							name: 'terms_page',
							type: 'Link',
							doctype: 'Web Page',
						},
						{
							label: 'Ask user category during signup',
							name: 'user_category',
							type: 'checkbox',
						},
						{
							type: 'Column Break',
						},
						{
							label: 'Show privacy policy on signup page',
							name: 'privacy_policy',
							type: 'checkbox',
						},
						{
							label: 'Privacy Policy Page',
							name: 'privacy_policy_page',
							type: 'Link',
							doctype: 'Web Page',
						},
						{
							type: 'Column Break',
						},
						{
							label: 'Show cookie policy on signup page',
							name: 'cookie_policy',
							type: 'checkbox',
						},
						{
							label: 'Cookie Policy Page',
							name: 'cookie_policy_page',
							type: 'Link',
							doctype: 'Web Page',
						},
					],
				},
			],
		},
		{
			label: 'Settings',
			hideLabel: true,
			items: [
				{
					label: 'Sidebar',
					icon: 'PanelLeftIcon',
					fields: [
						{
							label: 'Courses',
							name: 'courses',
							type: 'checkbox',
						},
						{
							label: 'Batches',
							name: 'batches',
							type: 'checkbox',
						},
						{
							label: 'Certified Participants',
							name: 'certified_participants',
							type: 'checkbox',
						},
						{
							type: 'Column Break',
						},
						{
							label: 'Jobs',
							name: 'jobs',
							type: 'checkbox',
						},
						{
							label: 'Statistics',
							name: 'statistics',
							type: 'checkbox',
						},
						{
							label: 'Notifications',
							name: 'notifications',
							type: 'checkbox',
						},
					],
				},
			],
		},
		{
			label: 'Settings',
			hideLabel: true,
			items: [
				{
					label: 'Email Templates',
					icon: 'MailPlus',
					fields: [
						{
							label: 'Batch Confirmation Template',
							name: 'batch_confirmation_template',
							doctype: 'Email Template',
							type: 'Link',
						},
						{
							label: 'Certification Template',
							name: 'certification_template',
							doctype: 'Email Template',
							type: 'Link',
						},
						{
							label: 'Assignment Submission Template',
							name: 'assignment_submission_template',
							doctype: 'Email Template',
							type: 'Link',
						},
					],
				},
			],
		},
		/* {
		label: 'Settings',
		hideLabel: true,
		items: [
		{
			label: 'Members',
			icon: "UserRoundPlus",
			component: markRaw(MemberSettings),
		},
		],
	}, */
	]

	return _tabs.map((tab) => {
		tab.items = tab.items.filter((item) => {
			if (item.condition) {
				return item.condition()
			}
			return true
		})
		return tab
	})
})

watch(show, () => {
	if (show.value) {
		activeTab.value = tabs.value[0].items[0]
	} else {
		activeTab.value = null
	}
})
</script>
