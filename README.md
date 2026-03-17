# Tests-fan

import bpy
import math

def create_ventilateur_XZ_positionne():
    # 1. Nettoyage de la scène
    bpy.ops.object.select_all(action='SELECT')
    bpy.ops.object.delete()

    # --- PARAMÈTRES ---
    centre = (0.32, 0.8, 0.4)    # Coordonnées (X, Y, Z)
    diametre_total = 0.24       
    longueur_pale = 0.10        
    largeur_pale = 0.034        
    angle_inclinaison = 30      
    nb_pales = 6                
    epaisseur = 0.002           
    rayon_moyeu = (diametre_total / 2) - longueur_pale 

    # Placer le curseur 3D au centre voulu (essentiel pour la rotation)
    bpy.context.scene.cursor.location = centre

    # 2. CRÉATION DU MOYEU (Centré sur les coordonnées demandées)
    bpy.ops.mesh.primitive_cylinder_add(
        radius=rayon_moyeu, 
        depth=0.02, 
        location=centre,
        rotation=(math.radians(90), 0, 0) # Axe de rotation Y
    )
    moyeu = bpy.context.active_object
    moyeu.name = "Moyeu_Y"

    # 3. CRÉATION DE LA PALE DE RÉFÉRENCE
    bpy.ops.mesh.primitive_cube_add(size=1.0)
    pale = bpy.context.active_object
    pale.name = "Pale_Ref"

    # Dimensions : Longueur sur X, Epaisseur sur Y, Largeur sur Z
    pale.scale[0] = longueur_pale / 2
    pale.scale[1] = epaisseur / 2
    pale.scale[2] = largeur_pale / 2
    bpy.ops.object.transform_apply(scale=True)

    # Positionnement relatif au centre : décalage sur X
    pale.location[0] = centre[0] + rayon_moyeu + (longueur_pale / 2)
    pale.location[1] = centre[1]
    pale.location[2] = centre[2]
    
    # Inclinaison de 30° par rapport au plan XZ
    pale.rotation_euler[0] = math.radians(angle_inclinaison)
    bpy.ops.object.transform_apply(rotation=True)

    # Déplacer l'origine de la pale au centre (0.32, 0.8, 0.4) pour la rotation circulaire
    bpy.ops.object.origin_set(type='ORIGIN_CURSOR')

    # 4. DUPLICATION CIRCULAIRE AUTOUR DE L'AXE Y
    for i in range(1, nb_pales):
        bpy.ops.object.duplicate(linked=True)
        nouvelle_pale = bpy.context.active_object
        
        # Rotation sur l'axe Y autour du point central
        rotation_y = (360 / nb_pales) * i
        nouvelle_pale.rotation_euler[1] = math.radians(rotation_y)

    # Sélection finale
    bpy.ops.object.select_all(action='SELECT')

# Exécution
create_ventilateur_XZ_positionne()
