<script lang="ts">
  import dayjs from "dayjs";
  import * as Svelte from "svelte";
  import cx from "classnames";

  export let dateString: string;

  const weekdayNames = ["日", "一", "二", "三", "四", "五", "六"];
  interface cellData {
    dateString?: string;
    date?: number;
    selected?: boolean;
    isThisMonth: boolean;
  }

  /**@summary Only values which directly appear within the $: block
   * will become dependencies of the reactive statement. */
  $: cells = handleCells(current, selectedDate);
  let selectedDate = dayjs(dateString).isValid ? dayjs(dateString) : dayjs();
  let current = dayjs(selectedDate);

  function handleCells(current: dayjs.Dayjs, selected: dayjs.Dayjs) {
    const startOfThisMonth = current.startOf("month").day();
    const prevMonth = current.subtract(1, "month");

    let temp = Array<cellData>(42);

    for (let i = 0; i < startOfThisMonth; i++) {
      temp[i] = {
        date: prevMonth.endOf("month").date() - startOfThisMonth + i + 1,
        isThisMonth: false,
      };
    }

    for (let i = 0; i < current.daysInMonth(); i++) {
      const str = `${current.year()}-${current.month() + 1}-${i + 1}`;
      temp[i + startOfThisMonth] = {
        dateString: str,
        date: i + 1,
        selected: dayjs(str).isSame(selected),
        isThisMonth: true,
      };
    }

    for (let i = startOfThisMonth + current.daysInMonth(); i < 42; i++) {
      temp[i] = {
        date: i - (current.daysInMonth() + startOfThisMonth) + 1,
        isThisMonth: false,
      };
    }

    return temp;
  }
</script>

<div class="calendar-box">
  <div class="year-month">
    <div
      class="arrow"
      on:click={() => (current = current.subtract(1, "month"))}
    >
      {"<"}
    </div>
    <div class="text">
      <span>{current.year()}</span> 年 <span>{current.month() + 1}</span> 月
    </div>
    <div class="arrow" on:click={() => (current = current.add(1, "month"))}>
      {">"}
    </div>
  </div>
  <div class="week">
    {#each weekdayNames as weekday}
      <div>{weekday}</div>
    {/each}
  </div>
  <div class="days">
    {#each cells as cell}
      <div
        class={cx(
          "day",
          { selected: cell?.selected },
          { isThisMonth: cell?.isThisMonth }
        )}
        on:click={() => {
          if (cell?.dateString) {
            selectedDate = dayjs(cell?.dateString);
          }
        }}
      >
        <span>{cell?.date || ""}</span>
      </div>
    {/each}
  </div>
</div>

<style lang="scss">
  $greenGrey: rgb(237, 241, 245);
  .calendar-box {
    width: 336px;
    padding: 5px;
    border: 1px solid $greenGrey;
    text-align: center;
    > div {
      width: 100%;
    }
    .year-month {
      padding: 5px 0;
      border-bottom: 1px solid $greenGrey;
      display: flex;
      justify-content: space-around;
      .arrow {
        width: 25px;
        background-color: darkseagreen;
        border-radius: 50%;
        color: #fff;
        cursor: pointer;
        &::selection {
          background-color: transparent;
        }
      }
    }
    .week {
      display: flex;
      border-bottom: 1px solid $greenGrey;
      > div {
        height: 48px;
        width: 48px;
        display: flex;
        flex-flow: column;
        justify-content: center;
      }
    }
    .days {
      display: flex;
      flex-flow: row wrap;
      .day {
        height: 48px;
        width: 48px;
        display: flex;
        justify-content: center;
        align-items: center;
        background-color: $greenGrey;
        border-bottom: 1px solid $greenGrey;
        box-sizing: border-box;
        color: #bbb;
        cursor: pointer;
        > span {
          height: 40px;
          width: 40px;
          line-height: 40px;
          border-radius: 50%;
          transition: 0.5s;
        }
        &.isThisMonth {
          background-color: #fff;
          color: darkslategrey;
        }
        &.selected {
          > span {
            background-color: darkseagreen;
            color: #fff;
          }
        }
      }
    }
  }
</style>
