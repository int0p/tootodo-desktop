<script lang="ts">
	import { DurationPicker } from "$components/schedule";
	import {
		type DateRange,
		getThisWeekRange,
		parseDateRangeFromURL,
	} from "$lib/utils";
	import { writable, type Writable } from "svelte/store";
	import { onMount, setContext } from "svelte";
	import { goto } from "$app/navigation";

	// duration select
	const selectedWeekRange: Writable<DateRange> = writable(getThisWeekRange());
	setContext("selectedWeekRange", selectedWeekRange);

	onMount(() => {
		$selectedWeekRange = parseDateRangeFromURL() || getThisWeekRange();
		setQuery($selectedWeekRange);
	});

	function setQuery(duration) {
		const start_date = duration.start;
		const end_date = duration.end;
		const searchParams = new URLSearchParams({ start_date, end_date });
		goto(`?${searchParams.toString()}`);
	}

	function handleDateUpdate(e) {
		const { selectedWeekRange } = e.detail;
		setQuery(selectedWeekRange);
		$selectedWeekRange = selectedWeekRange;
	}
</script>

<div class="flex flex-col justify-center w-full h-full sm:absolute sm:top-0">
	<!-- header: select date -->
	<div class="w-full h-10 translate-y-1">
		<DurationPicker on:update={handleDateUpdate} />
	</div>

	<div class="h-[calc(100%-60px)] w-full">
		<slot />
	</div>
</div>
