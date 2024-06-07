using System.Collections;
using NUnit.Framework;
using UnityEngine;
using UnityEngine.TestTools;

public class ProjectileLogicTest
{
    [UnityTest]

    public IEnumerator EnemyGetsDestroyedWhenHitByProjectile()
    {
        GameObject projectileGameObject = new GameObject();
        GameObject enemyGameObject = new GameObject();
        
        ProjectileLogic projectileLogic = projectileGameObject.AddComponent<ProjectileLogic>();
        CircleCollider2D enemyCollider = enemyGameObject.AddComponent<CircleCollider2D>();
        
        projectileLogic.detectionRadius = 2f;
        
        enemyGameObject.tag = "Enemy";
        projectileGameObject.tag = "Projectile";

        projectileGameObject.AddComponent<CircleCollider2D>();
        projectileGameObject.GetComponent<CircleCollider2D>().isTrigger = true;
        
        projectileLogic.nearestEnemy = enemyGameObject;
        
        projectileLogic.OnTriggerEnter2D(enemyCollider);
        
        yield return null;

        GameObject[] projectiles = GameObject.FindGameObjectsWithTag("Projectile");
        GameObject[] enemies = GameObject.FindGameObjectsWithTag("Enemy");
        
        Assert.AreEqual(0, projectiles.Length, "Projectile should be destroyed after collision.");
        Assert.AreEqual(0, enemies.Length, "Enemy should be destroyed when hit by projectile.");
        
        foreach (var projectile in projectiles)
        {
            GameObject.DestroyImmediate(projectile);
        }
        foreach (var enemy in enemies)
        {
            GameObject.DestroyImmediate(enemy);
        }
        GameObject.DestroyImmediate(projectileGameObject);
        GameObject.DestroyImmediate(enemyGameObject);
    }
}