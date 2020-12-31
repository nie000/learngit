 url --  
 path('index/', TitlesView.as_view({'get': 'list'}))


 view --
 class TitlesView(GenericViewSet, ListModelMixin):
    serializer_class = TitlesSerializer
    queryset = Titles.objects.all().order_by('-name')

serializers --
 class CommitsInfo(serializers.ModelSerializer):

    class Meta:
        model = Commits
        fields = '__all__'


class ArticlesSalaryInfo(serializers.ModelSerializer):
    commits_set = CommitsInfo(many=True, read_only=True)

    class Meta:
        model = Articles
        fields = ['name', 'commits_set', 'title', 'text']


class TitlesSerializer(serializers.ModelSerializer):
    articles_set = ArticlesSalaryInfo(many=True, read_only=True)

    class Meta:
        model = Titles
        fields = ['name', 'id', 'parent_id', 'articles_set']

model --
class Titles(models.Model):
    type_list = (
        (1, '一级标题'),
        (2, '二级标题'),
        (3, '三级标题')
    )
    name = models.CharField('标题', max_length=20)
    type = models.SmallIntegerField('标题类型', choices=type_list)
    sort = models.SmallIntegerField('排序', default=0)
    parent = models.ForeignKey('self', on_delete=models.CASCADE)


class Articles(models.Model):
    title = models.ForeignKey(Titles, verbose_name='所属文章',
                              on_delete=models.CASCADE)
    name = models.CharField('文章名称', max_length=20)
    text = models.CharField('文章内容', max_length=300)


class Commits(models.Model):
    article = models.ForeignKey(Articles, verbose_name='文章ip',
                                on_delete=models.CASCADE)
    commit_text = models.CharField('评论内容', max_length=20)
    x_cows= models.CharField('横坐标', max_length=20, default=10)
    # 可加纵坐标


 # sql -其他表数据自加
insert IGNORE  into `mysite_titles`(id,parent_id,name,type) values 
(1,0,'广东',1),   
(2,1,'珠海',2),   
(3,2,'片区1',3),  
(4,2,'片区2',3), 
(5,3,'街道1',4), 
(6,3,'街道2',4), 
(7,4,'街道2',4); 


# 后端数据获取效果 ,前端只需要根据id和pid,渲染树状结构
[
    {
        "name": "街道2",
        "id": 7,
        "parent_id": 4,
        "articles_set": []
    },
    {
        "name": "街道1",
        "id": 5,
        "parent_id": 3,
        "articles_set": []
    },
    {
        "name": "珠海",
        "id": 2,
        "parent_id": 1,
        "articles_set": []
    },
    {
        "name": "片区2",
        "id": 4,
        "parent_id": 2,
        "articles_set": []
    },
    {
        "name": "片区1",
        "id": 3,
        "parent_id": 2,
        "articles_set": []
    },
    {
        "name": "广东-首标题",
        "id": 1,
        "parent_id": 0,
        "articles_set": [
            {
                "name": "23",
                "commits_set": [
                    {
                        "id": 1,
                        "commit_text": "我是评论",
                        "x_cows": "12",
                        "article": 1
                    }
                ],
                "title": 1,
                "text": "我是文章"
            }
        ]
    }
]
