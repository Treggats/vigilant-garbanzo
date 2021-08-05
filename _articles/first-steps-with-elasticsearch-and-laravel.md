---
layout: article
title: First steps with Elasticsearch & Laravel
date: 2017-07-04
categories: [laravel,elasticsearch]
cover_image: https://cdn-images-1.medium.com/max/1024/1*VXdzDpO_KNegDIdMdO1OkA.png
---


After working with Solr in past employment, I’ve alway been interested in indexing large amounts of data and processing that.

So recently I was told to look into Elasticsearch and I’ve been following the [How to integrate your Laravel app with Elasticsearch](https://blog.madewithlove.be/post/how-to-integrate-your-laravel-app-with-elasticsearch) post by [Tony Messias](https://twitter.com/tony0x01) from [Made with love](https://madewithlove.be).

As it turns out that it’s actually quite simple, the Eloquent model that needs to be indexed has a trait which contains a created and deleted event. These are actually Model observer events, which determine if a piece of data should be indexed by Elasticsearch.

```php
public function saved ($model)
{
    $this->elasticsearch->index([
        'index' => $model->getSearchIndex(),
        'type' => $model->getSearchType(),
        'id' => $model->id,
        'body' => $model->toSearchArray(),
    ]);
}

public function deleted ($model)
{
    $this->elasticsearch->delete([
        'index' => $model->getSearchIndex(),
        'type' => $model->getSearchType(),
        'id' => $model->id,
    ]);
}
```

The first part was about getting data into Elasticsearch, the second part is about getting that data from Elasticsearch and presenting it on the Laravel side. Tony’s article is using a repository to interact with Elasticsearch. In that repository the following method is present to do the searching part.

```php
private function searchOnElasticsearch (string $query): array
{
    $instance = new Article;

    $items = $this->search->search([
        'index' => $instance->getSearchIndex(),
        'type' => $instance->getSearchType(),
        'body' => [
           'query' => [
              'multi\_match' => [
              'fields' => ['title', 'body', 'tags'],
                    'query' => $query,
                ],
            ],
        ],
    ]);

return $items;
}
```

It returns an array with the data it retrieved from Elasticsearch, which in turn can be used by Laravel as a data source.

So these were my first steps with Elasticsearch, I like how easy it actually was setting this up. Next is implementing this in a personal project, and trying to learn more about Elasticsearch in the meantime.
