# Health-Policy-Analyst-
A simple convolutional neural network (CNN) implemented in PyTorch that trains on the Fashion‑MNIST dataset and achieves **93.69% test accuracy** after 40 epochs. This repo includes scripts for data download, model training, evaluation, and inference on custom images.
- [Project Structure](#project-structure)
- 
import requests
import os
import subprocess

# --- Tənzimləmələr ---
SOURCE_USER = "rashaddd777"
TARGET_TOKEN = "YOUR_GITHUB_TOKEN" # Buraya öz tokenini yaz
TARGET_USER = "YOUR_GITHUB_USERNAME" # Buraya öz GitHub adını yaz

def run_command(command):
    subprocess.run(command, shell=True, check=True)

# 1. Mənbə profildəki bütün repozitoriyaları al
url = f"https://api.github.com/users/{SOURCE_USER}/repos"
repos = requests.get(url).json()

for repo in repos:
    repo_name = repo['name']
    clone_url = repo['clone_url']
    
    print(f"--- İşlənir: {repo_name} ---")

    # 2. GitHub-da sənin hesabında eyni adlı yeni repozitoriya yarat
    create_repo_url = "https://api.github.com/user/repos"
    headers = {"Authorization": f"token {TARGET_TOKEN}"}
    data = {"name": repo_name, "private": False}
    requests.post(create_repo_url, json=data, headers=headers)

    # 3. Klonla və Yenidən Yüklə
    # Qeyd: Bu hissə lokalda müvəqqəti qovluq yaradır
    run_command(f"git clone {clone_url} temp_repo")
    os.chdir("temp_repo")
    
    # Uzaq server ünvanını (remote) öz hesabına dəyiş
    new_remote = f"https://{TARGET_USER}:{TARGET_TOKEN}@github.com/{TARGET_USER}/{repo_name}.git"
    run_command(f"git remote set-url origin {new_remote}")
    
    # Kodları push et
    run_command("git push -u origin main") # və ya 'master'
    
    # Təmizlik
    os.chdir("..")
    run_command("rm -rf temp_repo")

print("Bütün repozitoriyalar uğurla köçürüldü!")
