<template>
    <a-card style="margin-left:250px;width:800px">
        <a-row>
            <a-col :md="24" :sm="48">
                <h1 style="font-size:26px;color:#3d9ccc">{{position.name}}</h1>
            </a-col>
        </a-row>
        <a-row>
            <a-col :md="24" :sm="48">
                <span style="font-size:16px;color:#3d9ccc">{{position.issueCompany}}</span>
            </a-col>
        </a-row>
        <a-row>
            <a-col :md="24" :sm="48">
                <span style="color:#ff7f00;font-size:32px">{{position.salary}}</span>
                <span style="color:#3d9ccc;font-size:12px">{{position.salary}}</span>
                <a-button type="primary" style="float:right;font-size:16px;background-color:#ffaa00">感兴趣</a-button>
            </a-col>
        </a-row>
        <a-row>
            <a-col :md="24" :sm="48" class="position-header">
                <ul>
                    <li style="font-size:16px;color:#333333">{{position.address}}</li>
                    <li style="font-size:16px;color:#333333">{{position.issueTimeDesc}}</li>
                </ul>
            </a-col>
        </a-row>
        <a-row>
            <a-col :md="24" :sm="48" class="position-tag">
                <a-tag color="blue">扁平管理</a-tag>
                <a-tag color="blue">管理规范</a-tag>
                <a-tag color="blue">五险一金</a-tag>
                <a-tag color="blue">团队聚餐</a-tag>
                <a-tag color="blue">岗位晋升</a-tag>            
            </a-col>
        </a-row>
        <a-divider />
        <div>
            <a-list
                bordered
                :dataSource="position.description"
            >
                <a-list-item slot="renderItem" slot-scope="item">{{item}}</a-list-item>
                <div slot="header">职责描述</div>
            </a-list>
        </div>
        <a-divider />
        <div>
            <a-list
                bordered
                :dataSource="position.requested"
            >
                <a-list-item slot="renderItem" slot-scope="item">{{item}}</a-list-item>
                <div slot="header">任职要求</div>
            </a-list>
        </div>
    </a-card>
</template>

<script>
export default {
    name:'Message',
    data() {
        return {
            position:{}
        }
    },
    methods: {
        getPosition () {
            let id = this.$route.query.name
            console.log(id)
            this.$get('position',id).then((r) => {
                this.loading = false
                this.position = r.data.rows[0]
                console.log(this.position)
            }).catch((e) => {
                console.error(e)
                this.$message.error('获取职位信息')
            })
        },
    },
    mounted() {
        this.getPosition()
    }
}
</script>

<style>
    .position-header{
        margin-top: 10 10 0;
    }
    .position-header ul{
        list-style:none; 
        margin: 0px;
        padding: 0;
        width: auto;
    }
    .position-header ul li{
        float:left;
        margin-right: 5px;
    }
    .position-tag{
        margin-top: 20px;
    }

</style>


