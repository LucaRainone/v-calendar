<template>
<div
  class='c-pane-container'
  :class='{ "is-double-paned": isDoublePaned_, "is-expanded": isExpanded }'
  :style='themeStyles_.wrapper'>
  <calendar-pane
    :position='isDoublePaned_ ? 1 : 0'
    :page.sync='fromPage_'
    :min-page='minPage'
    :max-page='maxFromPage'
    :styles='themeStyles_'
    :attributes='attributes_'
    @titleClick='titleClick'
    v-bind='$attrs'
    v-on='$listeners'>
  </calendar-pane>
  <calendar-pane
    v-if='isDoublePaned_'
    :position='2'
    :page.sync='toPage_'
    :min-page='minToPage'
    :max-page='maxPage'
    :styles='themeStyles_'
    :attributes='attributes_'
    @titleClick='titleClick'
    v-bind='$attrs'
    v-on='$listeners'>
  </calendar-pane>
</div>
</template>

<script>
import CalendarPane from './CalendarPane';
import Tag from './Tag';
import AttributeStore from '../utils/attributeStore';
import defaults from '../utils/defaults';
import {
  todayComps,
  pageIsEqualToPage,
  pageIsBeforePage,
  pageIsAfterPage,
  getPrevPage,
  getNextPage,
  getPageBetweenPages,
  getFirstValidPage,
} from '../utils/helpers';
import '../assets/fonts/vcalendar/vcalendar.scss';
import '../styles/lib.sass';

export default {
  components: {
    CalendarPane,
    Tag,
  },
  props: {
    minPage: Object,
    maxPage: Object,
    fromPage: Object,
    toPage: Object,
    isDoublePaned: Boolean,
    isExpanded: Boolean,
    themeStyles: Object,
    attributes: Array,
  },
  data() {
    return {
      windowWidth: 0,
      fromPage_: null,
      toPage_: null,
    };
  },
  computed: {
    isDoublePaned_() {
      return this.isDoublePaned && this.windowWidth >= 480;
    },
    paneCentered() {
      return this.isDoublePaned && !this.isDoublePaned_;
    },
    maxFromPage() {
      if (this.isDoublePaned_) return getPrevPage(this.maxPage);
      return this.maxPage;
    },
    minToPage() {
      if (this.isDoublePaned_) return getNextPage(this.minPage);
      return null;
    },
    themeStyles_() {
      // Mix user supplied styles with default styles
      return { ...defaults.themeStyles, ...this.themeStyles };
    },
    attributes_() {
      return AttributeStore(this.attributes);
    },
  },
  watch: {
    fromPage() {
      this.refreshFromPage();
    },
    toPage() {
      this.refreshToPage();
    },
    fromPage_(val) {
      this.$emit('update:frompage', val);
      if (!pageIsBeforePage(val, this.toPage_)) {
        this.toPage_ = getNextPage(val);
      }
    },
    toPage_(val) {
      this.$emit('update:topage', val);
      if (!pageIsAfterPage(val, this.fromPage_)) {
        this.fromPage_ = getPrevPage(val);
      }
    },
    isDoublePaned_() {
      this.refreshToPage();
    },
  },
  created() {
    this.handleResize();
    window.addEventListener('resize', this.handleResize);
    this.refreshFromPage();
    this.refreshToPage();
  },
  beforeDestroy() {
    window.removeEventListener('resize', this.handleResize);
  },
  methods: {
    refreshFromPage() {
      this.fromPage_ = getFirstValidPage(
        ...[
          this.fromPage,
          { month: todayComps.month, year: todayComps.year },
        ].map(p => getPageBetweenPages(p, this.minPage, this.maxPage)),
        this.minPage,
        getPrevPage(this.maxPage),
      );
    },
    refreshToPage() {
      this.toPage_ = getFirstValidPage(
        ...[
          this.toPage,
          getNextPage(this.fromPage_),
        ].map(p => getPageBetweenPages(p, this.minPage, this.maxPage)),
        this.maxPage,
        getNextPage(this.minPage),
      );
    },
    titleClick(page) {
      if (pageIsEqualToPage(page, todayComps)) return;
      if (page.position < 2) {
        this.fromPage_ = getFirstValidPage(todayComps, pageIsBeforePage(page, todayComps) ? this.maxFromPage : this.minPage);
      } else {
        this.toPage_ = getFirstValidPage(todayComps, pageIsBeforePage(page, todayComps) ? this.maxPage : this.minToPage);
      }
    },
    handleResize() {
      this.windowWidth = window.innerWidth;
    },
  },
};
</script>

<style lang='sass' scoped>

@import '../styles/vars.sass'

.c-container
  display: inline-flex
  flex-direction: column
  background-color: $paneBgColor
  border: $paneBorder
  &.center
    display: flex
    align-items: center

.c-pane-container
  flex-shrink: 1
  display: inline-flex
  min-width: $paneWidth
  width: $paneWidth
  background-color: $paneBgColor
  border: $paneBorder
  &.is-double-paned
    min-width: $paneWidth * 2
    width: $paneWidth * 2
  &.is-expanded
    width: 100%

.c-pane-divider
  width: 1px
  border: 1px inset
  border-color: #e3e3e3

</style>
