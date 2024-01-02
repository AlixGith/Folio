---
Title: Front War 2 - When the lead strikes the steel - C#/Javascript/traçabilité
Subtitle: ""
Date: 2023-12-14
Lastmod : 
Tags: ["Prog"]
image : "/img/collections/pikes_shots.jpg"
Description: "Article sur le dernier projet personnel."
Draft: 
---

# Novembre 2023 - *When the lead strikes the steel* - Jeu RTS 

*Dans la nostalgie et dans la volonté de continuer ma passion, je décide de reprendre les idées de mon premier projet de Jeu datant de 2016. - Jeu vidéo*

*Je souhaite suivre de bout en bout une rigueur de réalisation, que j'ai connu dans le milieu normatif : traçabilité, standardisation et conformité. Organigramme réalisé sous Visio :*
![Alt text](/img/collections/qualification.PNG "")

Objectifs :

- Jeu en Real Time strategy macroscopique.

- Vue orthographique et design retro (inspirations Cossacks:Art of War, Age of Empire 2 et empire earth).

- Ne doit pas depasser en taille l'espace de mon disque dur (libre) : 1 Go.
  
- Créer et faire évoluer son camp à travers l'une des époques les plus révolutionnaires au niveau technique et culturel : 16ème à la Fin du 17 ème - Epoque Pikes & Shottes - L'arrivée des européens en Amérique etc.
  
- Utilisation de bibliothèques gratuites et libre de droit.
  
- Traçabilité des exigences et arborescence : réaliser l'exigence "client" jusqu'à la ligne de code.
  
- Multijoueur avec serveur client-side + ressources graphiques côté client + Pre-rendering.  
  
- Mode de jeu "scalable" en fonction du nombre de joueur. 

- Open-source et première version qui sera sur github. 


*Le principe du cycle en V sera mis en place.  Cycle en V réalisé sous Visio :*
![Alt text](/img/collections/Cycle_en_v.PNG "")

*Server client-side. Schéma à revoir, simplement illustratif :*
![Alt text](/img/collections/reseau_spec.PNG "")

*Exigence du cahier de spécification logiciel concernant les interfaces/menus :*
![Alt text](/img/collections/interfaces.PNG "")

*Exigence du cahier de spécification logiciel concernant l'architecture de programmation, on définira ici le back-end comme ce qui ne se voit pas et le front-end comme ce qui se voit :*
![Alt text](/img/collections/Organigramme_p_2_page-0001.jpg "")
![Alt text](/img/collections/Organigramme_p_2_page-0002.jpg "")
![Alt text](/img/collections/Organigramme_p_2_page-0003.jpg "")


# Code pour la triangulation de delaunay et la recherche du point le plus proche

On utilise les librairies : UnityOctree et Triangle.NET

```

using UnityEngine;
using TriangleNet.Geometry;
using TriangleNet.Meshing;
using System.Collections.Generic;

public class Runtime_map_manager : MonoBehaviour 
{

    public List<Vector3> vertices;
    Octree octree;
    LineRenderer lineRenderer;

    void Update() 
    {
        // Create and populate the Octree
        octree = new Octree(10, Vector3.zero, 1);
        foreach (var vertex in vertices)
        {
            octree.Add(vertex);
        }

        // Perform Delaunay triangulation on the points
        InputGeometry input = new InputGeometry(vertices.Count);
        foreach (var vertex in vertices)
        {
            input.AddPoint(vertex.x, vertex.y, vertex.z);
        }

        var mesh = new StandardMesher().Triangulate(input);

        // Retrieve the vertices after triangulation
        vertices.Clear();
        foreach (var vertex in mesh.Vertices)
        {
            vertices.Add(new Vector3((float)vertex.X, (float)vertex.Y, (float)vertex.Z));
        }

        // Setup LineRenderer
        lineRenderer = gameObject.AddComponent<LineRenderer>();
        lineRenderer.material = new Material(Shader.Find("Particles/Standard Unlit"));
        lineRenderer.widthMultiplier = 0.2f;
        lineRenderer.positionCount = vertices.Count * 2;

        // Find closest points and draw lines
        for (int i = 0; i < vertices.Count; i++)
        {
            var closestPoint = octree.FindClosestPoint(vertices[i]);
            lineRenderer.SetPosition(i * 2, vertices[i]);
            lineRenderer.SetPosition(i * 2 + 1, closestPoint);
        }
    }
}

// HOW OCTREE Could work : 

// root is the top level node of the octreenode. Le code suivant est tiré d'un exemple de comment un octree peut fonctionner, il ne compile pas 

public Vector3 FindClosestPoint(Vector3 point)
{
    return FindClosestPoint(root, point, float.MaxValue, null);
}

private Vector3 FindClosestPoint(OctreeNode node, Vector3 point, float currentClosestSqrDistance, Vector3 currentClosestPoint)
{
    // If the node is further away than the current closest point, return the current closest point
    if (node.bounds.SqrDistance(point) > currentClosestSqrDistance)
    {
        return currentClosestPoint;
    }
    // Go through each point in the node and update the closest point if necessary
    foreach (var nodePoint in node.points)
    {
        var sqrDistance = (nodePoint - point).sqrMagnitude;
        if (sqrDistance < currentClosestSqrDistance)
        {
            currentClosestSqrDistance = sqrDistance;
            currentClosestPoint = nodePoint;
        }
    }
    // Go through each child of the node and recursively call this method
    foreach (var child in node.children)
    {
        currentClosestPoint = FindClosestPoint(child, point, currentClosestSqrDistance, currentClosestPoint);
    }
    return currentClosestPoint;
}
```


Logiciels / Outils : Unity 3D, Blender, mermaid, jira, confluence. 
