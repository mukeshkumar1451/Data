from fastapi import FastAPI
from ado_client import get_user_story

app = FastAPI()

@app.get("/userstory/{story_id}")
def fetch_user_story(story_id: str):
    story = get_user_story(story_id)
    return story
