<template>
	<span :title="convertDate">
		{{ format }}
	</span>
</template>

<script lang="ts">
import type { LocaleFunc } from 'timeago.js';
import { format, register } from 'timeago.js';
import { convertToHumanReadableDate } from '@/utils/typesUtils';
import { defineComponent } from 'vue';
import { mapStores } from 'pinia';
import { useRootStore } from '@/stores/n8nRoot.store';

export default defineComponent({
	name: 'TimeAgo',
	props: {
		date: {
			type: String,
			required: true,
		},
		capitalize: {
			type: Boolean,
			default: false,
		},
	},
	computed: {
		...mapStores(useRootStore),
		defaultLocale(): string {
			return this.rootStore.defaultLocale;
		},
		format(): string {
			const text = format(this.date, this.defaultLocale);

			if (!this.capitalize) {
				return text.toLowerCase();
			}

			return text;
		},
		convertDate(): string {
			const date = new Date(this.date);
			const epoch = date.getTime();
			return convertToHumanReadableDate(epoch);
		},
	},
	beforeMount() {
		register(this.defaultLocale, this.localeFunc as LocaleFunc);
	},
	methods: {
		localeFunc(_: number, index: number): [string, string] {
			// number: the timeago / timein number;
			// index: the index of array below;
			return [
				[this.$locale.baseText('timeAgo.justNow'), this.$locale.baseText('timeAgo.rightNow')],
				[this.$locale.baseText('timeAgo.justNow'), this.$locale.baseText('timeAgo.rightNow')], // ['%s seconds ago', 'in %s seconds'],
				[
					this.$locale.baseText('timeAgo.oneMinuteAgo'),
					this.$locale.baseText('timeAgo.inOneMinute'),
				],
				[this.$locale.baseText('timeAgo.minutesAgo'), this.$locale.baseText('timeAgo.inMinutes')],
				[this.$locale.baseText('timeAgo.oneHourAgo'), this.$locale.baseText('timeAgo.inOneHour')],
				[this.$locale.baseText('timeAgo.hoursAgo'), this.$locale.baseText('timeAgo.inHours')],
				[this.$locale.baseText('timeAgo.oneDayAgo'), this.$locale.baseText('timeAgo.inOneDay')],
				[this.$locale.baseText('timeAgo.daysAgo'), this.$locale.baseText('timeAgo.inDays')],
				[this.$locale.baseText('timeAgo.oneWeekAgo'), this.$locale.baseText('timeAgo.inOneWeek')],
				[this.$locale.baseText('timeAgo.weeksAgo'), this.$locale.baseText('timeAgo.inWeeks')],
				[this.$locale.baseText('timeAgo.oneMonthAgo'), this.$locale.baseText('timeAgo.inOneMonth')],
				[this.$locale.baseText('timeAgo.monthsAgo'), this.$locale.baseText('timeAgo.inMonths')],
				[this.$locale.baseText('timeAgo.oneYearAgo'), this.$locale.baseText('timeAgo.inOneYear')],
				[this.$locale.baseText('timeAgo.yearsAgo'), this.$locale.baseText('timeAgo.inYears')],
			][index] as [string, string];
		},
	},
});
</script>
