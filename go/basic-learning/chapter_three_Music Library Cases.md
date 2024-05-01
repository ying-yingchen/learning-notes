### chapter_three_Music Library Cases

#### 音乐库

##### 管理模块

管理对象：音乐

唯一的ID；

音乐名；

艺术家名；

音乐位置；

音乐文件类型（MP3和WAV等）。

##### 定义模块

```go
type Music struct{
    Id, Name, Artist, Source, Type string
}
```

##### 音乐库管理类型(manager.go)

通过数组切片作为基础存储结构

```go
package library
import {
    "errors"
}
type MusicManager struct{
    music []MusicEntry
}
func NewMusicManager() *MusicManager { 
 return &MusicManager{make([]MusicEntry, 0)} 
} 
func (m *MusicManager) Len() int { 
     return len(m.musics) 
} 
func (m *MusicManager) Get(index int) (music *MusicEntry, err error) { 
 if index < 0 || index >= len(m.musics) { 
 return nil, errors.New("Index out of range.") 
 } 
 return &m.musics[index], nil
} 
func (m *MusicManager) Find(name string) *MusicEntry { 
 if len(m.musics) == 0 { 
 return nil
 } 
 for _, m := range m.musics { 
 if m.Name == name { 
 return &m 
 } 
 } 
 return nil
} 
func (m *MusicManager) Add(music *MusicEntry) { 
 m.musics = append(m.musics, *music) 
} 
func (m *MusicManager) Remove(index int) *MusicEntry { 
 if index < 0 || index >= len(m.musics) { 
 return nil
 } 
 removedMusic := &m.musics[index] 
 // 从数组切片中删除元素
 if index <len(m.musics)-1 { // 中间元素
 m.musics = append(m.musics[:index-1],m.musics[index+1:]...) 
 } elseif index == 0 {    // 删除仅有的一个元素
 m.musics = make([]MusicEntry, 0) 
 } else {     // 删除的是最后一个元素
 m.musics = m.musics[:index-1] 
 } 
 return removedMusic 
} 
```

##### 音乐库管理类型的单元测试(manager_test.go )

```go
package library 
import ( 
"testing" 
) 
func TestOps(t *testing.T) { 
 mm := NewMusicManager() 
 if mm == nil { 
 t.Error("NewMusicManager failed.") 
 } 
 if mm.Len() != 0 { 
 t.Error("NewMusicManager failed, not empty.") 
 } 
 m0 := &MusicEntry{ 
 "1", "My Heart Will Go On", "Celion Dion", Pop, 
 "http://qbox.me/24501234", MP3} 
 mm.Add(m0) 
 if mm.Len() != 1 { 
 t.Error("MusicManager.Add() failed.") 
 } 
 m := mm.Find(m0.Name) 
 if m == nil { 
 t.Error("MusicManager.Find() failed.") 
 } 
 if m.Id != m0.Id || m.Artist != m0.Artist || 
 m.Name != m0.Name || m.Genre != m0.Genre || 
 m.Source != m0.Source || m.Type != m0.Type { 
 t.Error("MusicManager.Find() failed. Found item mismatch.") 
 } 
 m, err := mm.Get(0) 
 if m == nil { 
 t.Error("MusicManager.Get() failed.", err) 
 } 
 m = mm.Remove(0) 
 if m == nil || mm.Len() != 0 { 
 t.Error("MusicManager.Remove() failed.", err) 
 } 
} 
```

