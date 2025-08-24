# decky-lossless
🔹 Étape 1 : Préparer ton Steam Deck
Passe en Desktop Mode.
Installe Decky Loader (si pas déjà fait) → Decky GitHub.
Vérifie que tu as git, meson et ninja installés :
sudo pacman -S git base-devel meson ninja
🔹 Étape 2 : Installer lsfg-vk
Télécharge et compile lsfg-vk :
git clone https://github.com/PanCakeConnaisseur/lsfg-vk.git
cd lsfg-vk
meson setup build
ninja -C build
Copie la librairie buildée dans un dossier accessible, par exemple :
mkdir -p ~/.local/share/lsfg-vk
cp build/libvk_lsfg.so ~/.local/share/lsfg-vk/
🔹 Étape 3 : Script Decky (plugin simple)
On va créer un plugin Decky custom ultra simple qui permet :
activer/désactiver lsfg-vk,
choisir entre x2, x3, x4.
Dans Desktop Mode, va dans :
~/.local/share/decky/plugins/
Crée un dossier par ex. :
lsfg-manager
Dedans, crée un fichier main.py avec ce contenu :
import decky_plugin
import os

class Plugin:
    lsfg_path = os.path.expanduser("~/.local/share/lsfg-vk/libvk_lsfg.so")
    multiplier = 2

    async def enable_lsfg(self):
        os.environ["ENABLE_LSFG"] = "1"
        os.environ["VK_LAYER_PATH"] = self.lsfg_path
        os.environ["LSFG_MULTIPLIER"] = str(self.multiplier)

    async def disable_lsfg(self):
        os.environ.pop("ENABLE_LSFG", None)
        os.environ.pop("VK_LAYER_PATH", None)
        os.environ.pop("LSFG_MULTIPLIER", None)

    async def set_multiplier(self, mult: int):
        self.multiplier = mult
        os.environ["LSFG_MULTIPLIER"] = str(mult)
Ajoute un plugin.json pour que Decky l’affiche :
{
  "name": "LSFG Manager",
  "author": "Toi",
  "description": "Activer lsfg-vk et choisir x2/x3/x4",
  "main": "main.py",
  "version": "0.1.0"
}
