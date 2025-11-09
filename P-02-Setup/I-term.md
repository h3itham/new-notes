- Enable API 
	```txt
	Performace -> General -> Magic ->  Load Python API 
	```
- bluebook script 
```python 
# layout.py
import iterm2

async def main(connection):
    app = await iterm2.async_get_app(connection)
    window = await app.async_create_window()
    session = window.current_tab.current_session

    # Split vertically (left and right)
    right = await session.async_split_pane(vertical=True)

    # Focus back to left pane and split it horizontally (bottom)
    bottom = await session.async_split_pane(vertical=False)

iterm2.run_until_complete(main)

```
	