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
