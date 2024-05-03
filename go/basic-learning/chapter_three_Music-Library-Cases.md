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

##### 音乐播放模块

```go
//play.go
package mp 
import "fmt" 
type Player interface { 
 Play(source string) 
} 
func Play(source, mtype string) { 
 var p Player 
 switch mtype { 
 case "MP3": 
 p = &MP3Player{} 
 case "WAV": 
 p = &WAVPlayer{} 
 default:
 fmt.Println("Unsupported music type", mtype) 
 return 
 } 
 p.Play(source) 
} 
```

```go
//mp3.go
package mp 
import ( 
 "fmt" 
 "time" 
) 
type MP3Player struct { 
 stat int
progress int
} 
func (p *MP3Player)Play(source string) { 
 fmt.Println("Playing MP3 music", source) 
 p.progress = 0 
 for p.progress < 100 { 
 time.Sleep(100 * time.Millisecond) // 假装正在播放
 fmt.Print(".") 
 p.progress += 10 
 } 
 fmt.Println("\nFinished playing", source) 
}
```

##### 主程序(mplayer.go)

```go
package main 
import ( 
 "bufio" 
 "fmt" 
 "os" 
 "strconv" 
 "strings" 
 "pkg/mplayer/mlib" 
 "pkg/mplayer/mp" 
) 
var lib *library.MusicManager 
var id int = 1 
var ctrl, signal chan int
func handleLibCommands(tokens []string) { 
 switch tokens[1] { 
 case "list": 
 for i := 0; i < lib.Len(); i++ { 
e, _ := lib.Get(i) 
 fmt.Println(i+1, ":", e.Name, e.Artist, e.Source, e.Type) 
 } 
 case "add": { 
 if len(tokens) == 6 {
 id++ 
 lib.Add(&library.MusicEntry{strconv.Itoa(id), 
 tokens[2], tokens[3], tokens[4], tokens[5]}) 
 } else { 
 fmt.Println("USAGE: lib add <name><artist><source><type>") 
 } 
 } 
 case "remove": 
 if len(tokens) == 3 { 
 lib.RemoveByName(tokens[2]) 
 } else { 
 fmt.Println("USAGE: lib remove <name>") 
 } 
 default:
 fmt.Println("Unrecognized lib command:", tokens[1]) 
 } 
} 
func handlePlayCommand(tokens []string) { 
 if len(tokens) != 2 { 
 fmt.Println("USAGE: play <name>") 
 return 
 } 
 e := lib.Find(tokens[1]) 
 if e == nil { 
 fmt.Println("The music", tokens[1], "does not exist.") 
 return 
 } 
 mp.Play(e.Source, e.Type, ctrl, signal) 
} 
func main() { 
 fmt.Println(` 
 Enter following commands to control the player: 
 lib list -- View the existing music lib 
 lib add <name><artist><source><type> -- Add a music to the music lib 
 lib remove <name> -- Remove the specified music from the lib 
 play <name> -- Play the specified music 
 `) 
 lib = library.NewMusicManager() 
 r := bufio.NewReader(os.Stdin) 
for { 
 fmt.Print("Enter command-> ") 
 rawLine, _, _ := r.ReadLine() 
    line := string(rawLine) 
 if line == "q" || line == "e" { 
 break 
 } 
 tokens := strings.Split(line, " ") 
 if tokens[0] == "lib" { 
 handleLibCommands(tokens) 
 } elseif tokens[0] == "play" { 
 handlePlayCommand(tokens) 
 } else { 
 fmt.Println("Unrecognized command:", tokens[0]) 
 } 
 } 
} 
```

