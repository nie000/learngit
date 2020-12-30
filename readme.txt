--model --

class Titles(models.Model):
    type_list = (
        (1, '一级标题'),
        (2, '二级标题'),
        (3, '三级标题')
    )
    name = models.CharField('标题', max_length=20)
    type = models.SmallIntegerField('标题类型', choices=type_list)
    sort = models.SmallIntegerField('排序', default=0)
    parent = models.ForeignKey('self', on_delete=models.SET_NULL, null=True)


class Articles(models.Model):
    title = models.ForeignKey(Titles, verbose_name='所属文章',
                                 on_delete=models.SET_NULL, null=True)
    name = models.CharField('文章名称', max_length=20)
    text = models.CharField('文章内容', max_length=300)


class Commits(models.Model):
    article = models.ForeignKey(Articles, verbose_name='文章ip',
                                on_delete=models.SET_NULL, null=True)
    commit_text = models.CharField('评论内容', max_length=20)

-- sql 层级节点 --
insert  into `mysite_titles`(id,parent_id,name,type) values 
(1,0,'广东',1),   
(2,1,'珠海',2),   
(3,2,'片区1',3),  
(4,2,'片区2',3), 
(5,3,'街道1',4), 
(6,3,'街道2',4), 
(7,4,'街道2',4); 
