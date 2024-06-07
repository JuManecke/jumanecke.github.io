using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class ProjectileLogic : MonoBehaviour
{
    [SerializeField] public float speed = 4f; 
    [SerializeField] private float rotationSpeed = 10f; 
    [SerializeField] public float detectionRadius = 3f; 
    [SerializeField] private LayerMask enemyLayerMask; 

    public GameObject nearestEnemy; 

    void Start()
    {
        FindNearestEnemy();
    }

    void Update()
    {
        if (nearestEnemy != null)
        {
            MoveTowardsEnemy();
        }
        else
        {
            FindNearestEnemy();
            
            if (nearestEnemy == null)
            {
                Destroy(gameObject);
            }
        }
    }

    public void FindNearestEnemy()
    {
        Collider2D[] colliders = Physics2D.OverlapCircleAll(transform.position, detectionRadius, enemyLayerMask);
        float minDistance = Mathf.Infinity;

        foreach (Collider2D collider in colliders)
        {
            float distance = Vector2.Distance(transform.position, collider.transform.position);
            if (distance < minDistance)
            {
                minDistance = distance;
                nearestEnemy = collider.gameObject;
            }
        }
    }

    public void MoveTowardsEnemy()
    {
        Vector3 direction = (nearestEnemy.transform.position - transform.position).normalized;
        float angle = Mathf.Atan2(direction.y, direction.x) * Mathf.Rad2Deg;
        Quaternion targetRotation = Quaternion.AngleAxis(angle, Vector3.forward);
        transform.rotation = Quaternion.Slerp(transform.rotation, targetRotation, rotationSpeed * Time.deltaTime);
        
        transform.position = Vector2.MoveTowards(transform.position, nearestEnemy.transform.position, speed * Time.deltaTime);
    }

    public void OnTriggerEnter2D(Collider2D other)
    {
        if (other.gameObject == nearestEnemy)
        {
            Destroy(nearestEnemy);
            Destroy(gameObject);
        }
    }
}
