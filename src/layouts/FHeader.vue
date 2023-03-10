<template>
    <header class="f-header" :class="cCssClass">
        <div class="">
            <div class="row no-collapse align-items-center">
                <div class="col-2">
                    <router-link to="/" class="logo" :aria-label="$t('view_home.back_to_home')">
                        <img src="/logo.png" alt="" style="margin-left:20px" class="not-fluid">
                        <b class="testnet" v-show="isTestnet">Testnet</b>
                        
                    </router-link>
                </div>
                <div class="col right-col">
                    <f-navigation
                        :items="cNavigation"
                    ></f-navigation>
                    <f-dark-mode-switch ref="darkModeSwitch" />
                    <!-- <f-search-box ref="searchBox" class="small" expandable v-show="!cHomeView"></f-search-box> -->
                    <a class="ch-network" v-bind:href="urlExplorer" v-on:click="changeNetwork">{{ labelChangeNetwork }}</a>
                    <f-hamburger-switch
                        thickness="2"
                        two-lines
                        @hamburger-switch-on="onHamburgerSwitchOn"
                        @hamburger-switch-off="onHamburgerSwitchOff"
                    ></f-hamburger-switch>
                </div>
            </div>
        </div>

        <div class="f-drawer" @click="onDrawerClick">
            <div class="body"></div>
            <div class="footer">
                <f-social-media-links></f-social-media-links>
                <div class="copyright">
                    <a href="http://icicbchain.org" target="_blank" rel="nofollow">©2021 icicbchain.org</a>
                </div>
            </div>
        </div>
    </header>
</template>

<script>
    import FNavigation from "../components/FNavigation.vue";
    import FHamburgerSwitch from "../components/FHamburgerSwitch.vue";
    import FSocialMediaLinks from "../components/FSocialMediaLinks.vue";
    import { mapState } from 'vuex';
    // import FSearchBox from "../components/FSearchBox.vue";
    import FDarkModeSwitch from "@/components/FDarkModeSwitch.vue";
    
    const appConfig = require('../../app.config.js');
    
    export default {
        components: {
            FDarkModeSwitch,
            // FSearchBox,
            FNavigation,
            FHamburgerSwitch,
            FSocialMediaLinks
        },

        data() {
            const isTestnet = appConfig.useTestnet
            let labelChangeNetwork = ''
            let urlExplorer = '';
            if (isTestnet) {
                labelChangeNetwork = 'Switch to mainnet';
                urlExplorer = appConfig.explorer.mainnet
            } else {
                labelChangeNetwork = 'Switch to testnet';
                urlExplorer = appConfig.explorer.testnet
            }
            return {
                isTestnet,
                labelChangeNetwork,
                urlExplorer,
                /** Is drawer visible? */
                dDrawerOn: false
            }
        },

        computed: {
            /**
             * Get navigation by current language.
             *
             * @return {array}
             */
            cNavigation() {
                const messages = this.$i18n.messages[this.$i18n.locale];

                return messages.navigation || [];
            },

            /**
             * Container's css classes.
             *
             * @retun {object}
             */
            cCssClass() {
                return {
                    'drawer-on': this.dDrawerOn
                }
            },

            /**
             * Is current route home view?
             *
             * @retun {boolean}
             */
            cHomeView() {
                return (this.$route.name === 'home');
            },

            ...mapState(['breakpoints'])
        },

        watch: {
            /**
             * Watches for vue route change.
             */
            $route() {
                this.hamburgerSwitchOff();
            },

            /**
             * Watches for `breakpoints` state change.
             *
             * @param {object} _breakpoints
             */
            breakpoints(_breakpoints) {
                const menuMobileBreakpoint = _breakpoints['menu-mobile'];

                if (menuMobileBreakpoint && !menuMobileBreakpoint.matches) {
                    this.hamburgerSwitchOff();
                }
            }
        },
        methods: {
            moveNavigationToDrawer() {
                const { $refs } = this;

                if (!this.$el) {
                    return;
                }

                const eFNavigation = this.$el.querySelector('.f-navigation');
                const eDrawer = this.$el.querySelector('.f-drawer .body');

                if (eFNavigation && eDrawer) {
                    eDrawer.appendChild(eFNavigation);

                    if ($refs.darkModeSwitch) {
                        eDrawer.prepend($refs.darkModeSwitch.$el);
                    }
                }
            },

            moveNavigationFromDrawer() {
                const { $refs } = this;

                if (!this.$el) {
                    return;
                }

                const eFNavigation = this.$el.querySelector('.f-navigation');
                const eRightCol = this.$el.querySelector('.right-col');

                if (eFNavigation && eRightCol) {
                    eRightCol.insertBefore(eFNavigation, eRightCol.firstChild);
                    if ($refs.searchBox && $refs.darkModeSwitch) {
                        $refs.searchBox.$el.parentNode.insertBefore($refs.darkModeSwitch.$el, $refs.searchBox.$el);
                    }
                    // eRightCol.appendChild(eFNavigation);
                }
            },

            hamburgerSwitchOff() {
                this.$children.forEach(_child => {
                    if (_child.$options._componentTag === 'f-hamburger-switch') {
                        _child.dOn = false;
                    }
                });
            },

            onHamburgerSwitchOn() {
                this.moveNavigationToDrawer();
                this.dDrawerOn = true;
            },

            onHamburgerSwitchOff() {
                this.dDrawerOn = false;
                setTimeout(() => {
                    this.moveNavigationFromDrawer();
                }, 300);
            },

            onDrawerClick() {
                this.hamburgerSwitchOff();
            },

            changeNetwork() {
                const network = window.localStorage.getItem('graphql-network');
                if (network==='testnet') {
                    window.localStorage.setItem('graphql-network', 'mainnet');
                } else {
                    window.localStorage.setItem('graphql-network', 'testnet');
                }
                window.location.reload();
            }
        }
    }
</script>

<style lang="scss">
.dark-theme .f-header {background: var(--f-header-background-color)!important;}
    .f-header {
        --f-header-background-color: white;
        --f-header-link-color: #{$secondary-color-lighter};

        /*position: -webkit-sticky;*/
        /*position: sticky;*/
        position: fixed;
        top: 0;
        z-index: 10;
        width: 100%;
        // height: $f-header-height;
        color: #fff;
        background-color: #fbfcff;
        transition: height $transition-length ease;
        border-bottom: 1px solid transparent;


        .narrow-container {
            padding-top: 0;
            padding-bottom: 0;
            height: 100%;

            > .row {
                height: 100%;
            }
        }

        .logo {
            position: relative;

            .testnet {
                position: absolute;
                display: inline-block;
                padding: 0.25em 0.4em;
                font-size: 75%;
                font-weight: 700;
                line-height: 1;
                text-align: center;
                white-space: nowrap;
                vertical-align: baseline;
                border-radius: 0.25rem;
                color: #fff;
                background-color: #de4437;
            }

            > img {
                max-height: 44px;
                margin-bottom: 0 !important;
                transition: opacity $transition-length ease;
            }
        }

        .f-hamburger-switch {
            position: fixed;
            /*top: 12px;*/
            top: 16px;
            right: 16px;
            z-index: 11;
            transform: scale(0.9);
            display: none;
        }

        .f-drawer {
            position: fixed;
            z-index: 10;
            width: 100%;
            height: 100%;
            top: 0;
            padding: 64px 8px 8px 8px;
            background-color: var(--f-header-background-color);
            transform: translateX(100%);
            transition: all $transition-length ease;
            display: none;
            flex-direction: column;

            .body {
                flex: 1;
            }

            .footer {
                .f-social-media-links {
                    text-align: center;
                }
            }

            @include links() {
                color: var(--f-header-link-color);
                transition: color $transition-length ease;
            }

            a:not(.btn):hover {
                color: #fff;
                text-decoration: none;
            }

            .copyright {
                padding-top: 8px;
                text-align: center;
            }
        }

        .f-search-box {
            color: $body-color;
        }

        .right-col {
            //text-align: end;
            display: flex;
            align-items: center;
            justify-content: center;

            .f-navigation {
                margin-inline-end: 16px;
            }
        }

        .f-dark-mode-switch {
            margin-inline-end: 16px;
            margin-left: 50px;
        }

        &.drawer-on {
            .f-drawer {
                transform: translateX(0);
            }
        }

        a.ch-network {
            display: inline-block;
            margin: 0 10px;
            padding:0.3em 1em;cursor:pointer;
            border-radius: 20px;
            transition: all 250ms ease;
            &:hover {
                color: #fff;
                background-color: #de4437;
            }
        }
    }

    .search-box-on {
        .f-header {
            .right-col {
                padding-inline-end: 52px;
            }
        }
    }

    @include media-max($bp-menu) {
        .f-header {
            .logo {
                position: fixed;
                top: 4px;
                left: 16px;
                z-index: 11;

                > img {
                    max-height: 30px;
                }
            }

            &.drawer-on {
                .logo {
                    > img {
                        opacity: 0.5;
                    }
                }

                .f-search-box:not(.expanded) {
                    opacity: 0.5;
                }
            }

            .f-hamburger-switch {
                display: inline-block;

                &.on {
                }
            }

            .f-drawer {
                display: flex;
            }

            .right-col {
                .f-navigation {
                    display: none;
                    margin-inline-end: 0;
                }
            }

            .f-search-box {
                position: fixed;
                z-index: 12;
                top: 8px;
                left: 30%;
                margin-inline-end: 0;
                margin-inline-start: 0;
                /*transform: scale(0.5);*/

                &.small.expandable {
                    &:not(.expanded) {
                        input:not(.def):not([type=submit]).large {
                            background-color: transparent;
                            box-shadow: none;
                        }

                        button[type="submit"] {
                            /* color: #fff; */

                            &:hover {
                                background-color: transparent !important;
                            }
                        }
                    }
                }
            }

            .f-dark-mode-switch {
                display: none;
            }

            &.drawer-on {
                .f-navigation {
                    display: block;
                }

                .f-dark-mode-switch {
                    display: block;
                    text-align: end;
                    margin-inline-end: 8px;
                }
            }
            a.ch-network {
                margin-right: 60px;
            }
        }
    }
</style>
