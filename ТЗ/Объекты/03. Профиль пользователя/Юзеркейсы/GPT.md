Юзеркейсы профиля

1) Просмотр профиля по username
- GET /api/profiles/{username}/ → 200 { profile }

2) Редактирование профиля (владелец)
- PATCH /api/profiles/{id}/ → 200 { profile }

3) Подписаться / Отписаться
- PUT /api/profiles/{id}/follow/ → 200 { following:true, followers_count }
- DELETE /api/profiles/{id}/follow/ → 200 { following:false, followers_count }