<template>
    <div class="sidebar">
        <el-menu class="sidebar-el-menu" :default-active="onRoutes" :collapse="collapse" background-color="#324157"
            text-color="#bfcbd9" active-text-color="#00D1B2" unique-opened router>
            <template v-for="item in items">
                <template v-if="item.subs">
                    <el-submenu :index="item.index" :key="item.index">
                        <template slot="title">
                            <i :class="item.icon"></i><span slot="title">{{ item.title }}</span>
                        </template>
                        <el-menu-item v-for="(subItem,i) in item.subs" :key="i" :index="subItem.index">
                            {{ subItem.title }}
                        </el-menu-item>
                    </el-submenu>
                </template>
                <template v-else>
                    <el-menu-item :index="item.index" :key="item.index">
                        <i :class="item.icon"></i><span slot="title">{{ item.title }}</span>
                    </el-menu-item>
                </template>
            </template>
        </el-menu>
    </div>
</template>

<script>
    import bus from '../common/bus';
    export default {
        data() {
            return {
                collapse: false,
                items: [
                    {
                        icon: 'el-icon-setting',
                        index: 'dashboard',
                        title: '后台首页'
                    },
                    {
                        icon: 'el-icon-tickets',
                        index: 'table',
                        title: '用户列表'
                    },
                    {
                        icon: 'el-icon-message',
                        index: 'tabs',
                        title: '宠物猪列表',
                        subs: [
                            {
                                index: 'form',
                                title: '添加宠物猪'
                            },
                            {
                                index: 'editor',
                                title: '编辑宠物猪'
                            }
                        ]
                    },
                    {
                        icon: 'el-icon-date',
                        index: '3',
                        title: '宠物猪性格列表',
                        subs: [
                            {
                                index: 'form',
                                title: '添加宠物猪性格'
                            },
                            {
                                index: 'editor',
                                title: '编辑宠物猪性格'
                            }
                        ]
                    },
                    {
                        icon: 'el-icon-star-on',
                        index: 'charts',
                        title: '宠物猪等级',
                        subs: [
                            {
                                index: 'form',
                                title: '编辑宠物猪等级'
                            }
                        ]
                    },
                    {
                        icon: 'el-icon-rank',
                        index: 'drag',
                        title: '宠物猪基因分类列表'
                    },
                    {
                        icon: 'el-icon-warning',
                        index: 'permission',
                        title: '宠物猪基因等级列表'
                    },
                    {
                        icon: 'el-icon-error',
                        index: '404',
                        title: '事件分类列表'
                    },
                    {
                        icon: 'el-icon-rank',
                        index: '1',
                        title: '事件列表'
                    },
                    {
                        icon: 'el-icon-rank',
                        index: '1',
                        title: '属性列表'
                    },
                    {
                        icon: 'el-icon-rank',
                        index: '1',
                        title: '规则列表'
                    },
                    {
                        icon: 'el-icon-rank',
                        index: '1',
                        title: '规则分类列表'
                    },
                ]
            }
        },
        computed:{
            onRoutes(){
                return this.$route.path.replace('/','');
            }
        },
        created(){
            // 通过 Event Bus 进行组件间通信，来折叠侧边栏
            bus.$on('collapse', msg => {
                this.collapse = msg;
            })
        }
    }
</script>

<style scoped>
    .sidebar{
        display: block;
        position: absolute;
        left: 0;
        top: 70px;
        bottom:0;
    }
    .sidebar-el-menu:not(.el-menu--collapse){
        width: 250px;
    }
    .sidebar > ul {
        height:100%;
    }
</style>
