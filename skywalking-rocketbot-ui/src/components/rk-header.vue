/**
 * Licensed to the Apache Software Foundation (ASF) under one or more
 * contributor license agreements.  See the NOTICE file distributed with
 * this work for additional information regarding copyright ownership.
 * The ASF licenses this file to You under the Apache License, Version 2.0
 * (the "License"); you may not use this file except in compliance with
 * the License.  You may obtain a copy of the License at
 *
 *      http://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS,
 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * See the License for the specific language governing permissions and
 * limitations under the License.
 */

<template>
  <header class="rk-header flex-h">
    <div class="flex-h">
      <div class='logo-container'>
          <img :src="logo" alt="" srcset="">
      </div>
      <router-link class="nav-link mr-20" to="/" exact>
        <svg class="icon sm vm">
          <use xlink:href="#chart"></use>
        </svg>
        <span class="vm hide-xs ml-5">{{this.$t('dashboard')}}</span>
      </router-link>
      <router-link class="nav-link mr-20" to="/topology">
        <svg class="icon sm vm">
          <use xlink:href="#issues"></use>
        </svg>
        <span class="vm hide-xs ml-5">{{this.$t('topology')}}</span>
      </router-link>
      <router-link class="nav-link mr-20" to="/trace">
        <svg class="icon sm vm">
          <use xlink:href="#merge"></use>
        </svg>
        <span class="vm hide-xs ml-5">{{this.$t('trace')}}</span>
      </router-link>
      <router-link class="nav-link mr-20" to="/alarm">
        <svg class="icon sm vm">
          <use xlink:href="#spam"></use>
        </svg>
        <span class="vm hide-xs ml-5">{{this.$t('alarm')}}</span>
      </router-link>
    </div>
    <div class="flex-h">
      <a class="rk-btn mr-5 sm" :class="auto?'blue':'ghost'" @click="handleAuto">
        <span class="vm">{{this.$t('auto')}}</span>
      </a>
      <a class="rk-btn mr-15 sm ghost" @click="handleReload">
        <svg class="icon mr-5 vm" :class="{'loading': auto}">
          <use xlink:href="#retry"></use>
        </svg>
        <span class="vm">{{this.$t('reload')}}</span>
      </a>
      <a class="rk-header-user" v-clickout="handleHide">
        <div @click="handleShow" class="rk-btn" :class="show? 'blue' : 'ghost'">
          <svg class="icon vs">
            <use xlink:href="#user"></use>
          </svg>
          <svg class="icon vs">
            <use xlink:href="#arrow-down"></use>
          </svg>
        </div>
        <div class="rk-header-user-menu" v-if="show">
          <div class="rk-header-user-menu-i" @click="handleSignout">
            <svg class="icon vs">
              <use xlink:href="#issue-close"></use>
            </svg>
            {{this.$t('signout')}}</div>
        </div>
      </a>      
  </div>
  </header>
</template>

<script lang="ts">
import { Vue, Component } from 'vue-property-decorator';
import { Action, State, Getter } from 'vuex-class';
import timeFormat from '@/utils/timeFormat';

@Component
export default class Header extends Vue {
  @Getter('duration') private duration: any;
  @Action('SET_DURATION') private SET_DURATION: any;
  private show: boolean = false;
  private auto: boolean = false;
  private logo: string = '';
  private timer: any = null;
  private handleReload() {
    const gap = this.duration.end.getTime() - this.duration.start.getTime();
    const utcCopy: any = -(new Date().getTimezoneOffset() / 60);
    this.$emit('reloadFooter', [
      new Date(new Date().getTime() - gap),
      new Date(),
    ]);
  }
  private handleAuto() {
    this.auto = !this.auto;
    if (this.auto) {
      this.handleReload();
      this.timer = setInterval(this.handleReload, 6000);
    } else {
      clearInterval(this.timer);
    }
  }
  private handleHide() {
    this.show = false;
  }
  private handleShow() {
    this.show = !this.show;
  }
  private handleSignout() {
    localStorage.removeItem('skywalking-authority');
    this.$router.push('/login');
  }
  private mounted() {
    this.logo = require(`./../assets/img/logo.png`);
    this.$i18n.locale = 'zh';
  }
}
</script>

<style lang="scss" scoped>
.logo-container {
  height: 48px;
  width: 160px;
  float: left;
  img {
    height: 48px;
  }
}
.rk-header {
  flex-shrink: 0;
  justify-content: space-between;
  height: 48px;
  padding-right: 15px;
  padding-left: 15px;
  font-size: 13px;
  color: #efefef;
  z-index: 9;
  background-color: #252a2f;
  box-shadow: 0 1px 2px 0 rgba(26, 24, 29, 0.24);
  .svg-logo {
    width: 90px;
    height: 22px;
  }
  .rocketbot{
    padding-top: 27px;
    position: absolute;
    font-size: 11px;
  }
  .logo {
    font-family: "Avenir", Helvetica, Arial, sans-serif;
    font-size: 18px;
    padding-top: 2px;
    margin-right: 50px;
  }
  .nav-link {
    padding: 4px 10px;
    border-radius: 4px;
    opacity: 0.8;
    color: #efeff1;
    will-change: opacity, background-color;
    transition: opacity 0.3s, background-color 0.3s;
  }
  .nav-link:hover,
  .nav-link.active {
    opacity: 1;
    background-color: #333844;
  }
}
.rk-header-user{
  display: none;
  position: relative;
}
.rk-header-user-menu{
  position: absolute;
  top: 35px;
  right: 0;
  background-color: #fff;
  overflow: hidden;
  border-radius: 4px;
  padding: 3px 0;
  color: #333844;
  width: 100px;
  box-shadow: 0 1px 2px 0 rgba(0,0,0,0.1), 0 0 1px rgba(0,0,0,0.15);
}
.rk-header-user-menu-i{
  padding: 6px 10px;
  will-change: background-color;
  transition: background-color .3s;
  &:hover{
    background-color:#dededf; 
  }
}
</style>
