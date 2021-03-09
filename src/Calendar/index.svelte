<script lang="ts">
  import dayjs from "dayjs";
  import CalendarBox from "./CalendarBox.svelte";

  let showYearsBox = false;

  document.addEventListener("keydown", (e) => {
    if (e.key === "Tab" && showYearsBox) {
      showYearsBox = false;
    }
  });
  document.addEventListener("mousedown", (e) => {
    const t = e.target as HTMLElement;
    if (!t.classList.contains("year") && showYearsBox) {
      showYearsBox = false;
    }
  });
</script>

<div>
  <input
    type="text"
    placeholder={dayjs().format("YYYY-MM-DD")}
    on:focus={() => {
      showYearsBox = true;
    }}
  />
  <CalendarBox dateString={dayjs().format("YYYY-MM-DD")} />
</div>

<style lang="scss">
  div {
    width: 200px;
  }
</style>
