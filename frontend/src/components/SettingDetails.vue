<template>
	<div class="flex flex-col justify-between h-full p-8">
		<div class="flex space-x-10">
			<div v-for="(column, index) in columns" :key="index">
				<div class="flex flex-col space-y-4">
					<div v-for="field in column" :class="width">
						<Link
							v-if="field.type == 'Link'"
							v-model="field.value"
							:doctype="field.doctype"
							:label="field.label"
						/>
						<FormControl
							v-else
							:key="field.name"
							v-model="field.value"
							:label="field.label"
							:type="field.type"
						/>
					</div>
				</div>
			</div>
		</div>
		<div class="flex flex-row-reverse mt-auto">
			<Button variant="solid" :loading="data.save.loading" @click="update">
				{{ __('Update') }}
			</Button>
		</div>
	</div>
</template>

<script setup>
import { FormControl, Button } from 'frappe-ui'
import { computed, ref } from 'vue'
import Link from '@/components/Controls/Link.vue'

let width = ref('w-full')

const props = defineProps({
	fields: {
		type: Array,
		required: true,
	},
	data: {
		type: Object,
		required: true,
	},
})

const columns = computed(() => {
	const cols = []
	let currentColumn = []

	props.fields.forEach((field) => {
		if (field.type === 'Column Break') {
			if (currentColumn.length > 0) {
				cols.push(currentColumn)
				currentColumn = []
			}
		} else {
			if (field.type == 'checkbox') {
				field.value = props.data.doc[field.name] ? true : false
			} else {
				field.value = props.data.doc[field.name]
			}
			currentColumn.push(field)
		}
	})

	if (currentColumn.length > 0) {
		cols.push(currentColumn)
	}

	if (cols.length == 3) {
		width.value = 'w-64'
	} else {
		width.value = 'w-96'
	}

	return cols
})

const update = () => {
	props.fields.forEach((f) => {
		props.data.doc[f.name] = f.value
	})
	props.data.save.submit()
}
</script>
