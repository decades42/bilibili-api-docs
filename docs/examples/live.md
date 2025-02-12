# 示例：获取直播间弹幕和礼物

```python
from bilibili_api import live, sync

room = live.LiveDanmaku(22544798)

@room.on('DANMU_MSG')
async def on_danmaku(event):
    # 收到弹幕
    print(event)

@room.on('SEND_GIFT')
async def on_gift(event):
    # 收到礼物
    print(event)

sync(room.connect())
```

# 示例：简易录播

```python
from bilibili_api import live, sync
import aiohttp


async def main():
    # 初始化
    room = live.LiveRoom(3)
    # 获取直播流链接
    stream_info = await room.get_room_play_url()
    url = stream_info['durl'][0]['url']

    async with aiohttp.ClientSession() as sess:
        # 设置 UA 和 Referer 头以绕过验证
        async with sess.get(url, headers={"User-Agent": "Mozilla/5.0", "Referer": "https://www.bilibili.com/"}) as resp:
            # 以二进制追加方式打开文件以存储直播流
            with open('live.flv', 'ab') as f:
                while True:
                    # 循环读取最高不超过 1024B 的数据
                    chunk = await resp.content.read(1024)
                    if not chunk:
                        # 无更多数据，退出循环
                        print('无更多数据')
                        break
                    print(f'接收到数据 {len(chunk)}B')
                    # 写入数据
                    f.write(chunk)

sync(main())
```

