---
layout: post
---
### My ArchEnemy

NoReverseMatch today happened a couple of times, here's what I learnt:
1. whatever happens in url liquid tag, should happens in url.py reflecting on paths and diff names, AS WELL AS views.py function's arguments. Mainly it's the argument issue
2. 2 ways to passing arguments in liquid url tag: with or without '=', both are fine, I prefer without, simpler
3. argument passing order DOES matter, EVERYWHERE


### Other Notes
I spent most of the day debugging this searching function:
view.py
```python
# Q helps to filter through objects with options of 'or'
from django.db.models import Q
def search(request, query=None, topic_pk=None):
    # if no-input submission --> redirect to topics for users to browse
    if request.GET.get('query') == '':
        return redirect('forum:topics')

    # if user searching from nav bar, get the request query value stored properly 
    if query == None:
        query = request.GET.get('query')

    # search through all Post objects, in titles AND contents, because of Q
    search_result_posts = Post.objects.filter(
        Q(title__icontains=query) |
        Q(content__icontains=query) |
        Q(date_added__icontains=query))

    # because there are only a few topics, 
    # use filtered posts to determine topics for displaying in the sidebar
    search_result_topics = []
    for post in search_result_posts:
        if post.topic not in search_result_topics:
            search_result_topics.append(post.topic)

    # passing an empty topic if topic_pk=None
    search_result_topic = {}

    # if to check results in a specific topic
    if topic_pk != None:
        # make sure this topic exists and override the default value
        search_result_topic = get_object_or_404(Topic, pk=topic_pk)
        # search through all the posts in this topic, and override previous search_results_posts
        search_result_posts = search_result_topic.post_set.filter(
            Q(title__icontains=query) |
            Q(content__icontains=query))

    return render(request, 'forum/search.html', {
        'search_result_posts': search_result_posts,
        'search_result_topics': search_result_topics,
        'search_result_topic': search_result_topic,
        'query': query})
```

base.html
in attr action, the url points at 'forum:search'
```html
<form class="form-inline m-0 float-right"  action="{ url 'forum:search' }" method="GET">
	<input name="query" class="form-control mr-2" style="height:38px" type="text" placeholder="Topic Searcher...">
	<button class="btn my-2 ml-n5"  type="submit"><i class="fa fa-search"></i></button>
</form>
```
search.html
whereas in a link after the search results are rendered, the url points at 'forum:search_topic'
```html
<a class="btn btn-outline-secondary rounded-lg mb-2 p-2 shadow-sm" style="width:90%;"
   href="{ url 'forum:search_topic' query topic.pk }">{{ topic }}</a>
```
url.py
```python
path('search/', views.search, name='search'),
path('search/<query>/topic_<int:topic_pk>/', views.search, name='search_topic'),
```
**Note: \<query\> should be passed in path in the same order as it is in view function**