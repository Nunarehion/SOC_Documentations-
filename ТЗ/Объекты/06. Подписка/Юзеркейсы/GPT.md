Юзеркейсы подписок
- Подписка: PUT /api/profiles/{id}/follow/ → 200 { following:true }
- Отписка: DELETE /api/profiles/{id}/follow/ → 200 { following:false }
- Списки: GET /api/profiles/{id}/followers/?limit&after; GET /api/profiles/{id}/following/?limit&after
