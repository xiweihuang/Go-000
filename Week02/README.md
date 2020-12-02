### 我们在数据库操作的时候，比如 dao 层中当遇到一个 sql.ErrNoRows 的时候，是否应该 Wrap 这个 error，抛给上层。为什么，应该怎么做请写出代码？

> 在 dao 不直接Wrap这个sql.ErrNoRows的error，但是会抛一个 dao 自定义的error

## dao

```go
var ErrorNoRows = errors.New("no rows")

type User struct {

}

func GetUsers() ([]*User, error) {
	db, _ := sql.Open("", "")
	_, err := db.Query("select * from user where uid=?", uid)
	if err != nil {
		if errors.Is(err, sql.ErrNoRows) {
			return nil, ErrorNoRows
		}
		return nil, errors.Wrap(err, "get user failed")
	}

	// 处理逻辑
	return []*User{}, nil
}
```

## service

```go
func EditUsers() error {
	user, err := dao.GetUserById(1)
	if err != nil {
		if errors.Is(err, dao.ErrorNoRows) {
			// 业务逻辑，比如打印一下
			// 比如这里业务逻辑不关注查不到的情况，就不用再往上一级返回错误了

		} else {
			return err
		}
	}

	// xxx
	return nil
}
```